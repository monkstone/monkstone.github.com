---
layout: post
title: "Instance Of Pattern Matching"
date: 2021-10-16 06:00:00
categories: jruby_art update
keywords: JRuby, extension, java, polyglot, maven, instanceof, jdk17
---

Recently I've been exploring the goodies to come when I upgrade my ruby-processing projects to jdk17 (might happen this christmas or before if vanilla processing gets there 1st). Since JRuby14 we have:-

#### Pattern Matching for instanceof in Java ####

__From my Vec2D implementation in java__

See also [oracle tutorial][ref]

Original:-

```java

public double jx;

/**
 *
 * @param context ThreadContext
 * @param other IRubyObject
 * @return x IRubyObject
 */
@JRubyMethod(name = "x=")

public IRubyObject setX(ThreadContext context, IRubyObject other) {
    if (other instanceof RubyFloat) {
        jx = ((RubyFloat) other).getValue();
    } else {
        jx = ((RubyFixnum) other).getDoubleValue();
    }
    return other;
}
```
Since jdk14 looks safer, gets rid of ugly casting.

```java
/**
 *
 * @param context ThreadContext
 * @param other IRubyObject
 * @return y IRubyObject
 */
@JRubyMethod(name = "x=")

public IRubyObject setY(ThreadContext context, IRubyObject other) {
    if (other instanceof RubyFloat rubyFloat) {
        jx = rubyFloat.getValue();
    } else if (other instanceof RubyFixnum rubyFixnum) {
        jx = rubyFixnum.getDoubleValue();
    }
    return other;
}
```

Another attractive looking feature is switch expression which can be quite useful in simplifying code. [See oracle tutorial][oracle], article to follow here.

[ref]:https://blogs.oracle.com/javamagazine/post/pattern-matching-for-instanceof-in-java-14/

[oracle]:https://docs.oracle.com/en/java/javase/15/language/switch-expressions.html
