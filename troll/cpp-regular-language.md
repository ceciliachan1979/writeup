# Answer

**Creation time:** Apr 25, 2020 11:57 PM PDT

**Content language:** English

## Question

Is C++ a regular language?

## Content

No, it is not.

Consider this valid c++ program:

```cpp
int main(int argc, char** argv)
{
    int a = (1);
    return 0;
}
```

Now you could add any number of matching parenthesis around that 1.

The pumping lemma will conclude that the language is not regular.

>To make the argument precise, intersect C++ with a regular language that fixes all the text before and after `(1)`, but allows any number of opening parentheses before the `1` and any number of closing parentheses after it, independently. The valid programs in this intersection are exactly those with matching counts. The pumping lemma shows that this intersection is not regular: for sufficiently many opening parentheses, the part to be pumped lies either in the fixed prefix or entirely among the opening parentheses, or crosses their boundary. Pumping changes the required prefix or changes the opening count without changing the closing count. If C++ were regular, its intersection with a regular language would also be regular. So C++ is not regular.
