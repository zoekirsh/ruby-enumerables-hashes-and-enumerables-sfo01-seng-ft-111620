# Hashes and Enumerables

## Learning Goals

- Use `each` and `each_pair` to print out a `Hash`
- Use `reduce` to create a transformed `Hash`
- Use `reduce` to resolve a value from a `Hash`

## Introduction

While this module has focused on using Enumerables within `Array`s, `Hash`es
_also_ have _all the same_ Enumerables &mdash; although some are less useful!

With a `Hash`, we will not use `map`. Instead, we'll use `reduce`, passing
`reduce` a _new_ `Hash` to populate as its argument. Inside `reduce`'s block,
we'll add to that new-`Hash` argument (the first block parameter, traditionally
called `memo`), _returning_ the `memo` at the end of each block.

> **LIFE-SAVING BUG AVOIDING TIP**: Working with `reduce` can be tricky many
> programmers forget to return the `memo` at the end and have a tricky bug they
> can't figure out!

Sometimes, before starting our `reduce` it's helpful to work with the `Hash`,
the `each` and the `Hash`-relevant `each_pair` methods can help us get a handle
on the situation.

For the remainder of this lesson, we'll be using the following `Hash` as a data
source.

```ruby
bands = {
  joy_division: %w[ian bernard peter stephen],
  the_smiths: %w[johnny andy morrissey mike],
  the_cramps: %w[lux ivy nick],
  blondie: %w[debbie chris clem jimmy nigel],
  talking_heads: %w[david tina chris jerry]
}
```

## Use `each` and `each_pair` to Print Out a `Hash`

For a simple exercise that `each` is perfect for, let's print out each pair:

```ruby
bands.each{ |pair| p pair } #=>
# [:joy_division, ["ian", "bernard", "peter", "stephen"]]
# [:the_smiths, ["johnny", "andy", "morrissey", "mike"]]
# [:the_cramps, ["lux", "ivy", "nick"]]
# [:blondie, ["debbie", "chris", "clem", "jimmy", "nigel"]]
# [:talking_heads, ["david", "tina", "chris", "jerry"]]
```

We can see that the block has an Array with two arguments: the key and the
value _yielded_ to it.

A more _expressive_ synonym is `each_pair`, which does the same thing:

```ruby
bands.each_pair{ |pair | p pair } #=>
# [:joy_division, ["ian", "bernard", "peter", "stephen"]]
# [:the_smiths, ["johnny", "andy", "morrissey", "mike"]]
# [:the_cramps, ["lux", "ivy", "nick"]]
# [:blondie, ["debbie", "chris", "clem", "jimmy", "nigel"]]
# [:talking_heads, ["david", "tina", "chris", "jerry"]]
```

Just like with an `Array`, if you want to do a transformation, you're better
off using `reduce`.

## Use `reduce` to Create a Transformed `Hash`

Let's put all our bands' members' names in order and print the original and
sorted Hashes. But reduce behaves a little funnily in `Hash`es so lets memorize
a tricky bit of syntax.

```ruby
bands = {
  joy_division: %w[ian bernard peter stephen],
  the_smiths: %w[johnny andy morrissey mike],
  the_cramps: %w[lux ivy nick],
  blondie: %w[debbie chris clem jimmy nigel],
  talking_heads: %w[david tina chris jerry]
}

bands.reduce({}) do |memo, pair|
  p memo # First block parameter
  p pair # Second block parameter
  memo # Return value for the block, becomes the memo in the next go-round
end
{}
[:joy_division, ["ian", "bernard", "peter", "stephen"]]
{}
[:the_smiths, ["johnny", "andy", "morrissey", "mike"]]
{}
[:the_cramps, ["lux", "ivy", "nick"]]
{}
[:blondie, ["debbie", "chris", "clem", "jimmy", "nigel"]]
{}
[:talking_heads, ["david", "tina", "chris", "jerry"]]
```

As we can see, our "accumulating" `Hash` called `memo` is the thing we need to
update. But in each call to our block we receive the pair as a two-element
`Array`. We'd like to split that into the `key` and the `value`. While we could
use `pair[0]` and `pair[1]`, Ruby provides a nicer way to do that work called
"destructuring assignment."

```ruby
bands = {
  joy_division: %w[ian bernard peter stephen],
  the_smiths: %w[johnny andy morrissey mike],
  the_cramps: %w[lux ivy nick],
  blondie: %w[debbie chris clem jimmy nigel],
  talking_heads: %w[david tina chris jerry]
}

bands.reduce({}) do |memo, (key, value)|
  p memo # First block parameter
  p key # Second block parameter
  p value # Second block parameter
  memo # Return value for the block, becomes the memo in the next go-round
end

#=>
# {}
# :joy_division
# ["ian", "bernard", "peter", "stephen"] ... etc.
```

Thanks to destructuring assignment (using the parentheses), we crack open the
`Array` that was in the `pair` parameter and put element `0` in `key` and
element `1` in `value`. With this in place, it's easy to create that
alphabetized roster.

```ruby
bands = {
  joy_division: %w[ian bernard peter stephen],
  the_smiths: %w[johnny andy morrissey mike],
  the_cramps: %w[lux ivy nick],
  blondie: %w[debbie chris clem jimmy nigel],
  talking_heads: %w[david tina chris jerry]
}

sorted_member_list =  bands.reduce({}) do |memo, (key, value)|
  memo[key] = value.sort
  memo
end

p bands
p sorted_member_list
{:joy_division=>["ian", "bernard", "peter", "stephen"], :the_smiths=>["johnny",
"andy", "morrissey", "mike"], :the_cramps=>["lux", "ivy", "nick"],
:blondie=>["debbie", "chris", "clem", "jimmy", "nigel"],
:talking_heads=>["david", "tina", "chris", "jerry"]}

# Space added by us for readability

{:joy_division=>["bernard", "ian", "peter", "stephen"], :the_smiths=>["andy",
"johnny", "mike", "morrissey"], :the_cramps=>["ivy", "lux", "nick"],
:blondie=>["chris", "clem", "debbie", "jimmy", "nigel"],
:talking_heads=>["chris", "david", "jerry", "tina"]}
```

Amazing!

## Use `reduce` to Resolve a Value From a `Hash`

With `Hash`es, we don't use `reduce` to transform them, we can also accumulate
to a single value. Let's find first-most alphabetical band member of the entire
`Hash`

```ruby
bands = {
  joy_division: %w[ian bernard peter stephen],
  the_smiths: %w[johnny andy morrissey mike],
  the_cramps: %w[lux ivy nick],
  blondie: %w[debbie chris clem jimmy nigel],
  talking_heads: %w[david tina chris jerry]
}

firstmost_name = bands.reduce(nil) do |memo, (key, value)|
  # On the first pass, we don't have a name, so just grab the first one.
  memo = value[0] if !memo

  # Sort that array of names
  sorted_names = value.sort

  # If string comparison says the sorted name of the array is earlier than
  # the memo, it becomes the new memo.
  memo = sorted_names[0] if sorted_names[0] <= memo

  # Return the memo as per reduce rules
  # (Try adding 'p' in front of memo to see how it changes as the enumerate the
  # pair of the hash!)
  memo
end
p firstmost_name

"andy"
```

## Conclusion

With Hash, the most common Enumerables are `each` and `reduce`. Mastering using
`reduce` to transform a given `Hash` into a new `Hash` is a sign of a truly
comfortable Ruby programmer.

With this last lesson under your belt, all you need in order to become that
"truly comfortable Ruby programmer" is a lot of practice. We're going to give a
quiz quiz to help you test your understanding and then we're going to give you
lots of labs to practice!

## Resources
