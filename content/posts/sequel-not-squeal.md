---
title: "Sequel Like A Hog(i) Not Squeal Like A Pig"
date: 2019-04-06T018:30:14-06:00
draft: false
---

# Sequel Like A Hog(i), Not Squeal Like A Pig


> SUMMARY:  In this post I will illustrate a creative work around for a scenario 
>           we had to deal with related to prepared statements in Sequel with
>           an array of values.   

Recently I had been working on some big refactoring of one o our apps.  As apps
mature and become heavily used, sometimes we might feel a performance hit.  In
our app we were noticing some delays on the database side of things.  The code
was a mixture of means and methods of querying using the Sequel gem.

I was working on converting all of our queries to make sure that we used bind
variables and prepared statements.  However I hit a bit of a snag with a few
queries I needed as my input data is a ruby array.  To add a little mud to the
situation, the input arrays are of varying sizes.

What is a HOG(i) to do?  Racked my brain for a bit and chatted up the DB pro of
our team.  He presented the basis for what you will find below.  I took the meat
of his ideas and baked them into the code below.  Since I had a need to use this
new method in many places of our code, I whipped up a modular, re-usable, and
fairy dry fix!


```
module SequelChunker

  CHUNK_LIMIT = (ENV['RACK_ENV'] == 'production') ? 50 : 5

  def extract_count(qry_result)
    count = 0

    qry_result.each do |key, value|
      count += key[:count]
    end

    count
  end

  def process_chunked_qry(records, qry_stmt)
    result_set = []

    return result_set if qry_stmt.nil? || records.empty?

    while records.count > 0 do
      chunk = records.pop(CHUNK_LIMIT)

      # If our chunk isn't big enough, fill it in with NULLs
      chunk.fill(nil,chunk.length,CHUNK_LIMIT) if chunk.length < CHUNK_LIMIT

      # Convert an array of values into a hash: {:p1 => "value1", :p2 => "value2", ... :pN => "valueN}
      chunk = Hash[(1..CHUNK_LIMIT).map { |i| ('p'+i.to_s).to_sym }.zip(chunk)]

      result_set += qry_stmt.call(:select, chunk)
    end
    result_set
  end

  def qry_in_clause
    return @qry_in_clause if instance_variable_defined?(:@qry_in_clause)
    @qry_in_clause = ( 1..CHUNK_LIMIT).map { |i| ":p#{i}" }.join(',')
  end

  def qry_placeholders
    return @qry_placeholders if instance_variable_defined?(:@qry_placeholders)
    @qry_placeholders = Hash[(1..CHUNK_LIMIT).map { |i| ["p#{i}".to_sym, "$p#{i}".to_sym ]}]
  end

end
```

You do not need to use this code as a module, I am just presenting it this way 
for readability as well to keep the code organized.  Feel free to place it where
you need it.  However, the example usuage below assumes we are using the module.

Ok, this seems like a nice *chunk* <pun> of code, but can you provide an example
of how one might use this?  Yes, yes I can!

```
gem install sequel
gem install sqlite3

➜  ruby irb
2.5.3 :001 > require 'sequel'
 => true 
2.5.3 :002 > require 'sqlite3'
 => true 
2.5.3 :003 > require_relative 'sequel_chunker'
 => true 
2.5.3 :004 > include SequelChunker
 => Object 
2.5.3 :005 > CHUNK_LIMIT
 => 2 
2.5.3 :006 > DB = Sequel.sqlite
 => #<Sequel::SQLite::Database: {:adapter=>:sqlite}> 
2.5.3 :007 > qry_in_clause
 => ":p1,:p2" 
2.5.3 :008 > qry_placeholders
 => {:p1=>:$p1, :p2=>:$p2} 
2.5.3 :009 > nurny_id_qry = DB["select nurny_id from docker_nodes where name IN (#{qry_in_clause})", qry_placeholders]
 => #<Sequel::SQLite::Dataset: "select nurny_id from docker_nodes where name IN (`$p1`,`$p2`)"> 
2.5.3 :010 > 
```

The above example is using a CHUNK_LIMIT of 2.  Let's bump that up to 5 and take another look.

```
➜  ruby irb  
2.5.3 :001 > require 'sequel'
 => true 
2.5.3 :002 > require 'sqlite3'
 => true 
2.5.3 :003 > require_relative 'sequel_chunker'
 => true 
2.5.3 :004 > include SequelChunker
 => Object 
2.5.3 :005 > CHUNK_LIMIT
 => 5 
2.5.3 :006 > qry_in_clause
 => ":p1,:p2,:p3,:p4,:p5" 
2.5.3 :007 > qry_placeholders
 => {:p1=>:$p1, :p2=>:$p2, :p3=>:$p3, :p4=>:$p4, :p5=>:$p5} 
2.5.3 :008 > DB = Sequel.sqlite
 => #<Sequel::SQLite::Database: {:adapter=>:sqlite}> 
2.5.3 :009 > nurny_id_qry = DB["select nurny_id from docker_nodes where name IN (#{qry_in_clause})", qry_placeholders]
 => #<Sequel::SQLite::Dataset: "select nurny_id from docker_nodes where name IN (`$p1`,`$p2`,`$p3`,`$p4`,`$p5`)"> 
2.5.3 :010 > 
```


Let me wrap up this piggy for today and present one more example - one that is 
very close to what I'm using in our app:

```
   nurny_id_qry = DB["select nurny_id from docker_nodes where name IN
                       (#{qry_in_clause})", qry_placeholders]

   qry_result = process_chunked_qry(dkr_container_list, nurny_id_qry)
   return nil unless qry_result.is_a?(Array) && !qry_result.empty?

   nurny_id = qry_result.first
```


HoGi...

*Learning something new every day*
