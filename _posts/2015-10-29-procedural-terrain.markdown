---
layout: post
title:  "Procedural terrain generation."
date:   2015-10-29 21:34:24
categories: opengl procedural
---

For two years know i'm experimenting with procedural generation. I think
it's time to share some of my tests and results in this first post.

I first was on a "small side project" about making a procedural rpg like
the story mode of dwarf fortress (yeah that sounds false). For contextuals reasons the project 
has started in Java (with lwjgl) and the first results where quite promising :

![After some opengl](/images/procedural_terrain_1.jpg)
![After some more opengl](/images/procedural_terrain_2.jpg)

##How it works

The first thing to figure out was what to procedurally generate. We quickly
made the decision to use a volumetric (instead of a heightmap) approach
to allow to generate hangovers and caves. Then the question was how to generate those
volumes and display theme. Here noises come to the rescue !

##Noises

Coherent noises are functions of one or more variables that return a pseudo random
__continuous__ value. The continuous part is the most important one. Cause 
you don't want your terrain to have overly abrupt cliffs or crazy spikes
in all directions. The second great property of these noises is that they are
virtualy infinite. You can evaluate theme for a very long time before you
can identify a pattern (as long as you combine them with severals of their own
octaves). They are indeed perfect for never endless terrains and borderless
worlds.

#Basic implementation

{% highlight java %}
/**
 * NoiseGenerator is an interface that defines what a NoiseGenerator should be
 * 
 * @author gregwar
 * @author farenjihn
 *
 */
public interface NoiseGenerator {
	
	/**
	 * Returns the value of the specific Noise at the coordinates x, y, z
	 * @param x
	 * @param y
	 * @param z
	 * @return double value
	 */
	public abstract double valueAt(double x, double y, double z);

}
{% endhighlight %}

Quite simple, this interface is key part of the world gen process. It abstract
a 3D noise. The terrain generator itself then takes one of thoses noise as
input to know where there is earth or not.

A single noise isn't really pleasing to see. To generate beautiful landscapes
you must composite many noises with different frequencies and gain values.

In fact the NoiseGenerator That we give to the World is a specified noise that
takes noises and add them. The more noises are added, the more natural the result.

