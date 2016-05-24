+++
date = "2011-10-11T20:00:50+01:00"
draft = false
title = "Silverlight and the ConvertAll method"

+++

I may have already mentioned my woes (here or on Twitter) with the Linq2SQL in a Silverlight for Windows Phone Mango environment.

I am of the opinion that the following code should work:

```
public class Person : IPerson
{   
    // Class internals, not relevant for this example.
}

public class DataContext
{
    public DataContext()
    {
        // Load data or whatever.
    }

    public Table<Person> People { get; set; }

    public IEnumerable<IPerson> GetPeople()
    {
        return this.People;
    }
}
```

I would have assumed this would work, because `Table<T>` implements the `IEnumerable<T>` interface and the contents of the Table, in this instance Person - implements the IPerson interface.

But apparently it wouldn't. I cannot recall now if it returned an `IEnumerable<T>` that was empty, or if it was just null. In either case it wasn't working.

<!--more-->

So after a little investigation I found a blog post [1] showing you 4 increastingly better ways for doing what I wanted. However I could only get Method 1 to work, because Silverlight does not support the .ConvertAll method...

I cannot easily find any information why this method wouldn't be available in the Silverlight runtime, one quick read did suggest it was just something that was dropped to make the runtime more lightweight - which seems reasonable I guess, but it didn't seem too heavy an idea in my mind. But whatever.

So this is what I ended up with, hardly elegant - but it worked:

```
	public class Person : IPerson
	{
	    // Class internals, not relevant for this example.
	}

	public class DataContext
	{
	    public DataContext()
	    {
	        // Load data or whatever.
	    }

	    public Table<Person> People { get; set; }

	    public IEnumerable<IPerson> GetPeople()
	    {
	        IEnumerable<IPerson> people = new List<IPerson>();
	        foreach (Person p in this.People)
	            ((List<IPerson>)people).Add(p as IPerson);

	        return people;
	    }
	}
```

Wanting a more elegant solution that didn't mean that I would be writing foreach loops in all of my Get methods - I went on the hunt for something to replace the .ConvertAll method. I found a cool littl blog post [2] that described creating a Silverlight array helper that would implement a static .ConvertAll method that could be used.

Following that as an example my code can now look like:

	public class Person : IPerson
	{
	    // Class internals, not relevant for this example.
	}

	public class DataContext
	{
	    public DataContext()
	    {
	        // Load data or whatever.
	    }

	    public Table<Person> People { get; set; }

	    public IEnumerable<IPerson> GetPeople()
	    {
	        return ConvertAll<Person, IPerson>(this.People, new Converter<Person, IPerson>(p => (IPerson)p));
	    }

	    // A re-usable method to ConvertAll items in an IEnumerable... 
	    // probably best if pulled out into a helper class.
	    public static IEnumerable<TOutput> ConvertAll<TInput, TOutput>(IEnumerable<TInput> enumerable, Converter<TInput, TOutput> converter)
	    {
	        return (from item in enumerable select converter(item)).AsEnumerable<TOutput>();
	    }
	}

I'll be interested to see what kind of affect this might have on performance (the target platform for this Silverlight application is Windows Phone 7.5, so performance is important to keep things lightweight, fast and fluid). But I will cross that bridge when I come to it, in the meantime at least my code is cleaner.

### References:

[1] - http://devlicio.us/blogs/derik_whittaker/archive/2008/03/28/simple-way-to-convert-ienumerable-lt-entity-gt-to-list-lt-ientity-gt.aspx

[2] - http://michaelsync.net/2009/03/22/silverlight-3-array-helper