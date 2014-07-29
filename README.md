This is the *example code* for a blog post at http://phvalues.wordpress.com/2014/07/29/more-fun-with-rakefiles/ ‎

This code is meant for **reading** more than executing, since it recreates a bug in a rake task as described in the blog post.
Since the described bug leads to more and more Ruby processes being started on a machine, it may bring that machine to a grinding halt.

And that's why I recommend AGAINST actually running it.

**After** un-commenting the lines 29-32 in the Rakefile, the task *should* be safe to run. 
But then it doesn't do anything very interesting anymore.
