---
layout: post
title:  "Reading Eloquent Ruby Part 1"
date:   2022-12-12 11:45:00 +0100
categories: rubyonrails
---

[Reminders on Parameters](#reminders-on-parameters)  
[The Everything is an Object'ness of Ruby](#the-everything-is-an-objectness-of-ruby)  
[Thoughts on the Object'ness of Ruby](#thoughts-on-the-objectness-of-ruby)  
[Stubbing](#stubbing)   
[Some Notes on Clean Code](#some-notes-on-clean-code)   
[Wrapping up](#wrapping-up)  

### Intro

With this first post on this blog, I start in true chaos queen fashion. 
The voice memo I did after the reading was on a self-destructing message timer, so I lost all the things I noted to myself, anyways :woman-shrugging:  
After some frustrations about having forgotten most of high school math, I decided to pick up "Eloquent Ruby", by our queen Sandi Metz, to feel better about myself.

### Reminders on Parameters 

With the rails app we develop at work we use mainly instance variables and thus have very little parameters in our methods. I'm not so happy with that as it can get rather hard to maintain a clear view of what comes from where and what it holds. For me it  was nice to be reminded of: 

{% highlight ruby %}
def load_font( name, size = 12) 
  # size has a default value of 12
end

# this accepts any number of arguments
def echo_all( *args )
  args.each { |arg| puts arg }
end

# to call the method for an elemnt of each element in a collection 
array = [1,2,3,4,5]
echo_all( *array ) #=> puts 1, puts 2 ...

{%endhighlight%}

### The Everything is an Object'ness of Ruby

This was interesting to me because I often struggled with very long chains of associations in the views. For example when calling something like `user.posts.pluck[:title].... ` some users might not have a post and then you are trying to call things on the class Nil which does not have this method and end up with errors. 

There are two things about this. Firstly you shouldn't be doing this in the firstplace and have a view model that provides one viewmodel object for a view which then puts the logic and the chains into a model. While the same code still raises the same error, it leads to less of these errors.   
Secondly the `try(:symbol)` method can help and is a substitute for the safe navigation operator in the Rails context.

Another aspect in the chapter on objectness is the eval command. This seemed really useful for meta programming to me and I thought about using it in a parent class I'm currently writing for an event log. This way I could define a hash of what method to call for some class and have a very clean Presenter / ViewModel

{% highlight ruby %}
command = "say_hello"
some_object.eval(command) 
#=> calls say_hello on some_object if it implements it, 
#=> if it doenst implement it raises error
{%endhighlight%}

### Thoughts on the Object'ness of Ruby 

I'm honestly still a little confused on what is considered to be the right way to work with and not against rubys dynamic typing. My feeling at the moment is that i try to much to make things work with meta programming and in that try to do a lot of type checking or specific delegations.  
Rather than approaching it like that I should move towards having parent and subclasses which implement methods differently and trust the ducktyping.

### Stubbing 

So we made a commitment at work to get out test coverage up. We started writing some rspec tests with the new features, and when fixing bugs wrapping the older code in tests as well. However i quickly realized that my tests where beginning to grow a lot, as our application is about the interaction of many states of objects. This lead to my tests growing quite large only by having to jump through the hoops of creating all those needed objects to get to my expectation. At that point i gave up my hope of our tests acting as documentation. 

In the chapter regarding good tests or tests in the ruby way i was happy to learn that stubbing would solve my issues. By creaing a Astub of an object you can define a placeholder object and what its supposed to return on certain method calls.

{% highlight ruby %}
it 'should send an email if the adress is correct' do
  student_stub = stub :address_probable? => true, :render => nil 
  app = build(:application, student: student_stub) # factory bot
  app.send_mail # send mail checks for its students address 

  # ... 
end
{% endhighlight %}

### Some Notes on Clean Code

Some weeks ago I watched a video about using less nesting, and structuring the blocks into guard clauses and code.   
Ruby is also really helpful for this approach as blocks such as while can be put on the end of a line like `if`s:  

{% highlight ruby %}
  document.print_next_page until document.printed?
{%endhighlight%}

### Wrapping up

I think a lot of questions that I have left open on good OO-design are due to my confusions about OOP.  
In order to improve and learn more I want to try and implement Design Pattern from the Design Patterns book, and read more OOP code.

