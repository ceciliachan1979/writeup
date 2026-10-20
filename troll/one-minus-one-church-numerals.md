# Answer

**Creation time:** Jun 17, 2023 09:48 AM PDT

**Content language:** English

## Question

How do you prove $1-1=0$?

## Content

Once upon a time very long ago, there was a church. Those were the days when the natural numbers of not invented yet. Odd enough, these people are very intelligent that they are about to invent math.

There is a bell in the church, and there is a hammer. You hit the hammer on the bell, it rings once. People liked the sound, and they are going to invent music soon, maybe …

A man named Alonzo came by and he built this little machine. If you give it a hammer and a bell, it will hit the hammer on the bell once and so the bell ring once.

The machine knows nothing about the hammer and the bell. If you give it a hammer and a man, then oh no, … content snipped, imagine yourself …

The machine is an abstraction of the concept one. It has nothing to do with a hammer or a bell, it is apply the input device to the input object once.

Soon people built other curious machines. A machine that that a hammer and a bell but don’t hit it at all, or hit it twice. They could, in theory, create any non-negative integers with it.

Except it gets very tedious, imagine building a machine that hit the bell a 100 times.

A man named Curry came by and he built this another machine, it doesn’t take a hammer, and it doesn’t ring a bell. It takes in two machines and chain them together to create a new machine.

When the new machine is fed with a hammer and a bell, it will first put the hammer and the bell in the first machine, take out the bell, put the hammer and the bell in the second machine.

And there you go, you have addition. The innovation is this machine creating machines.

Once you have addition, then it is natural to ponder about subtraction, that got people in the church stuck for a long while.

How exactly can I get the hammer to ‘unhit’ a bell? They really can’t - if the bell rang, it already did.

Haskell came by and figure this idea that rang the bell (pun intended) … what if we put the bell in a box instead. The first hammering only break the box, then the second hammering and ring the bell, we can have the number subtracted by exactly one.

That doesn’t go very well, the hammer is designed to ring the bell, it doesn’t know what to do with the box, it blows up.

But the idea get passed on to Howard. Howard invented this guardian of the box, that helps protect the box as follows:

You create a guardian of the box with a hammer (or anything that you will want to apply to the bell), now the guardian have the hammer.

Whenever you apply a guardian of the box to a box, it does the following.

1. It opens the box
2. It apply the given tool (i.e. the hammer) to the object inside the box.
3. Pack the object in another box.

So the box keep intact - the guardian of the box saved the Galaxy (pun intended)

Haskell noticed that - but Howard’s Guardian is useless. It doesn’t solve the decrement problem. It just do a lot more busy work opening and closing the boxes.

They sat together and come up with this new idea, the lazy box, that actually works.

There are two types of boxes, the normal box and the lazy box.

The normal box can take a tool, and when you apply the normal box:

1. It opens the box,
2. Apply the tool to the object in the box, and
3. Seal the object in another normal box.

The lazy box do exactly the same, except step 2

1. It opens the box, and
2. Seal the object in another normal box

Since now the box apply the tool, the guardian of the box don’t have to, all it does is tell the box to apply.

And so, for $n \ge 1$, if we use the guardian of the box trick on the bell initially sealed in the lazy box, it will skip exactly the first time, turn into a normal box, and hit exactly $n - 1$ times, perfect.

The last detail is that the output of the machine would be the bell inside a box, that’s fine, we can create a one last step to just take the bell outside of either type of box, and we are done.

There is a tricky special case in the above, can you spot it?

To see this is action, let’s start proving $1 - 1 = 0$

To start with we have a bell, we put it in a lazy box.

The hit once machine use the guardian to apply the lazy box.

The lazy box unwrap the bell, and then wrap the bell back in a normal box.

The hit once machine is done, it outputs a bell in the normal box.

The final step take the bell outside of the box, and we are done.

During the whole process, the bell rang exactly $0$ times, so $1 - 1 = 0$

QED.

Special thanks to Alonzo Church, Curry, Haskell and Howard for playing along. Welcome to the world of Church Numeral, Currying and Curry–Howard correspondence.

Church thesis claims that anything you can compute can be represented with only these little machines that take input and output and nothing else. We just saw we can represent numbers, but we can also represent a lot of other things as well.

Exercise: How would you create a machine that do multiplication, arbitrary subtraction, representing negative numbers, and so on …

[Church encoding — Church numerals](https://en.wikipedia.org/wiki/Church_encoding#Church_numerals)
