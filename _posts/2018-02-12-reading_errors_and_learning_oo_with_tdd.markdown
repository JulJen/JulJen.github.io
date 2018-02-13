---
layout: post
title:      "Reading Errors and learning OO with TDD"
date:       2018-02-12 21:03:53 -0500
permalink:  reading_errors_and_learning_oo_with_tdd
---


Back when I started the section about *[Object Relationships](https://learn.co/tracks/full-stack-web-development-v4/object-oriented-ruby/object-relationships/intro-to-object-relationships)* section of this curriculum, I spent some time  (...A LOT of TIME) trying to understand how objects interact and how "one object can belong to many".  So far I've been solving labs through Learn IDE  by reading the tests first and then writing methods to see if the tests pass. In order to better understand object relationships in Ruby, I wanted to learn about how objects interface with one another from a test-driven level.  

I will practice writing the tests before writing any methods - I did this following one of the [learn-instruct](http://instruction.learn.co/student/home) OO video lectures using [atom text editor](https://atom.io/) and [terminal (Mac command line)](https://www.davidbaumgold.com/tutorials/command-line/). This is also a good way for me to learn how to read error messages. At the beginning it was difficult to get a grasp of what all the errors messages meant. I would refer back to the [lesson on debugging ](https://learn.co/tracks/full-stack-web-development-v4/intro-to-ruby-development/debugging/reading-error-messages)in the Intro To Ruby Development section and the video lecture on [Learn Instruct](http://instruction.learn.co/student/video_lectures#/179).

It's always easier to start with a topic that you enjoy so my theme will be on "climbing". 
This is an example of a “has-many-through” relationship:

```
 Mountaineer
   has_many mountains
   has_ many sherpas through mountains

 Mountain
    belongs to a mountaineer
    belongs to a sherpa

 Sherpa
   has_many mountains
   has_many mountaineers, through mountains
```



* Given a 'mountaineer', I want to be able to **call a method** called 'mountains' and **expect back instances** of 'mountain'. 
```
hillary = Climber.new
hillary.mountains #=> [#<Mountain>, #<Mountain>] 
```

Next, I create a spec file for each object in my domain, I run $`rspec --init` to create a new local .rspec and a .spec_helper file, and then $`touch mountaineer_spec.rb, sherpa_spec.rb, and expedition_spec.rb`.  

* Now I can start creating my tests in each spec file:
```
 describe Mountaineer do
   it 'has a name' do
     mountaineer = Mountaineer.new
     mountaineer.name = "Edmund Hillary" 

     expect(mountaineer.name).to eq("Edmund Hillary") 
   end
 end
```
```
 describe Mountain do
   it 'has a name' do
     mountain = Mountain.new
     mountain.name = "Mount Everest" 

     expect(mountain.name).to eq("Mount Everest") 
   end
 end
```
```
 describe Sherpa do
   it 'has a name' do
     sherpa = Sherpa.new
     sherpa.name = "Tenzing Norgay"

     expect(sherpa.name).to eq("Tenzing Norgay")
   end
end
```



I run $`rspec --f-f` to see my failures. My first errors return that there is a `NameError` and an `uninitialized constant`. 

* What is a **[NameError](http://ruby-doc.org/core-2.3.0/NameError.html)**?

* What is an **[uninitialized constant](https://www.thoughtco.com/nameerror-uninitialized-2907928)**?

* **Solution:**  create a class  for 'mountaineer', 'mountain', and 'sherpa'. 

I run $`rspec --f-f` and still see the same errors of `NameError` and an `uninitialized constant`. 

* **Solution:** my spec_helper.rb doesn't know anything about my new files in the `lib` folder. In my terminal, I enter $`mkdir config`, then $`cd config` and $`touch environment.rb`. Using my text editor, in my environment.rb file, add the following lines: 
```
require_relative '../lib/mountaineer'
require_relative '../lib/mountain'
require_relative '../lib/sherpa'
```

* **My spec helper also needs to be made aware require of my environment.rb file**, 
so in spec/spec_helper.rb: `require_relative '../config/environment'`


I run $`rspec --f-f` and no more failures. Now I can  begin to write tests to explore the interface of these objects. Back in my mountaineer_spec.rb,  I am describing 'mountains' with 'mountaineers'.  I expect 'mountaineer' to respond to a method called 'mountains'  and I expect it to match the array of an empty array. Calling methods on an object is called an **interface**.   

* Here, the method I am **defining** is 'mountains' and I am trying to **expose** how 'mountaineers' have 'mountains'. 
```
   describe Mountaineer do
    it 'has a name' do
      mountaineer = Mountaineer.new
      mountaineer.name = "Edmund Hillary"

      expect(mountaineer.name).to eq("Edmund Hillary")
    end

    context 'with mountain' do
      describe '#mountains' do
        it 'has an empty array of mountains when initialized' do
          mountaineer = Mountaineer.new
          expect(mountaineer.mountain).to match_array([])
        end
      end
    end
end
```

I run $`rspec --f-f`, read the error message and begin to solve my next failure. 
* Based on my new updated mountaineer_spec.rb, I need to set 'mountains' to an empty array. 
```
 Failures:
 1) Mountaineer with mountain #mountains has an empty array of mountains when initialized
 Failure/Error: expect(mountaineer.mountain).to match_array([])

 NoMethodError:
 undefined method `mountain' for #<Mountaineer:0x007f8f920353d0 @mountains=[]>
 Did you mean?  mountains
 # ./spec/mountaineer_spec.rb:13:in `block (4 levels) in <top (required)>'

 Finished in 0.00294 seconds (files took 0.19884 seconds to load)
 3 examples, 1 failure
```

My next requirement is to figure out how do you give a 'mountaineer' a 'mountain' ?  

* By pushing anything other than a 'mountain', it should **raise an error**. This is where I learn how to define a custom error type in my environment.rb. Back in my mountaineer_spec.rb:
```
     describe 'add_mountain' do
       it 'it can push mountain instances onto it' do
         mountaineer = Mountaineer.new
         mountain = Mountain.new
 
         mountaineer.add_mountain(mountain)

         expect(mountaineer.mountains).to include(mountain)
       end

       it 'only allows mountains to be pushed onto it' do
         mountaineer = Mountaineer.new
         mountain = "Mount Everest"

         expect{mountaineer.add_mountain(mountain)}.to raise_error(AssociationTypeMismatchError
       end
     end
   end
end
```

* In my environment.rb file, I also add the following line:
`class AssociationTypeMismatchError < TypeError; end`

* In my mountaineer.rb file, I add the following lines of code:
```
 class Mountaineer
   attr_accessor :name

   def initialize
     @mountains = []
   end

   def mountains 
     @mountains
   end
   
   def add_mountain(mountain) #has_many interface
     raise AssociationTypeMismatchError, "#{mountain.class} received, Mountain expected" if !mountain.is_a?(Mountain)
     @mountains << mountain
   end
end
```

**What I learned at the beginning of this video walk-through:**

* Only write enough code to make each test pass

* Objects collaborating  on a TD level

* Define **[custom error messages ](https://ruby-doc.org/core-2.5.0/Exception.html)**

* What is an **[environment?](http://blog.honeybadger.io/ruby-guide-environment-variables/)**

* Running $`rspec --format=documentation` , returns **[RSpec](http://rspec.info/about/)** in the documentation format. This is helpful to understand why each string in the spec file creates a more readable test:

```
 Mountain
   has a name

 Mountaineer
   has a name
   with mountain
     #mountains
       has an empty array of mountains when initialized
     add_mountain
       it can push mountain instances onto it
       only allows mountains to be pushed onto it

 Sherpa
   has a name

 Finished in 0.00508 seconds (files took 0.21448 seconds to load)
 6 examples, 0 failures
```
