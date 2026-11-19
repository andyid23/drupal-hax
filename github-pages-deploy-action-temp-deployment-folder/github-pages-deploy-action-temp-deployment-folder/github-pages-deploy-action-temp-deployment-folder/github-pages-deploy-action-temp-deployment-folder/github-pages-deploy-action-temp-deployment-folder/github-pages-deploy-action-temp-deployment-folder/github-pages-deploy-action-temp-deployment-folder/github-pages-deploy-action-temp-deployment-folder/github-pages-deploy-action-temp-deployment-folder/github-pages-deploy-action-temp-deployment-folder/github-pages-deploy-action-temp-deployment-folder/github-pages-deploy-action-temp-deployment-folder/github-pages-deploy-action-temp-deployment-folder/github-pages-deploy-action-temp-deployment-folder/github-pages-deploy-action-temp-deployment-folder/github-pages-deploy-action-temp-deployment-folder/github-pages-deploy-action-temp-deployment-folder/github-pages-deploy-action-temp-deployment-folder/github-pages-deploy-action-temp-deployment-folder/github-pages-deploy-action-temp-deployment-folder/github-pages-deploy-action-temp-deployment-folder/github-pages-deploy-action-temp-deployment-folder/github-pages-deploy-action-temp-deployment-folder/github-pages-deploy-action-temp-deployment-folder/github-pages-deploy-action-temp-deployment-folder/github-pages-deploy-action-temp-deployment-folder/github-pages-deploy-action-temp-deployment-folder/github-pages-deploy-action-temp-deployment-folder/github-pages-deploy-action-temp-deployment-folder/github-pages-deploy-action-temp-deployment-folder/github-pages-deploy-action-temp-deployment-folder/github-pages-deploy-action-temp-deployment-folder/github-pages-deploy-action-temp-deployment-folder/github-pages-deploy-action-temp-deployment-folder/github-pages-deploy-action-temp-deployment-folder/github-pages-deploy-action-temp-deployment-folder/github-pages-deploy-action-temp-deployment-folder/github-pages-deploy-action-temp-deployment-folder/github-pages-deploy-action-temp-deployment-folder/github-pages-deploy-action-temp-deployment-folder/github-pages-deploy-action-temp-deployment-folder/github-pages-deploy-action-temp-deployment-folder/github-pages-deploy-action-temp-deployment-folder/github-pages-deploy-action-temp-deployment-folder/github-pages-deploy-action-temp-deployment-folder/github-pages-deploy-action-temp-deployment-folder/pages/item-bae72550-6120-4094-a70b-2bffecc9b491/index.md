Practice: more functions
------------------------

### Multiple choice

What will the following output, if the user enters 2, 3, and 5?

*   \# Input
*   laugh\_rating1 = float(input('How funny was the first comedian? '))
*   laugh\_rating2 = float(input('How funny was the second comedian? '))
*   laugh\_rating3 = float(input('How funny was the third comedian? '))

*   \# Processing
*   average = compute\_average(laugh\_rating1, laugh\_rating2, laugh\_rating3)

*   \# Output
*   print('Average: '+ str(average))

*   def compute\_average(value1, value2, value3):
*       total = value1 + value2 + value3
*       average = total/3
*       return average

**A**

3

**B**

3.333333

**C**

4

**D**

Error

Not graded. [So why do it?](#/ "Why you should do this")

### Reflect

Write a docstring for this function.

*   def predict\_bitcoin\_price(today\_price):
*       five\_years = today\_price / 2
*       ten\_years = 0
*       return today\_price, five\_years, ten\_years

_If you were logged in as a student, the lesson would pause here, and you'd be asked to type in a response. If you want to try that out, ask for an account on this site._

![Ethan](https://python.skilling.us/sites/default/files/skilling/characters/ethan-smile1.png)  
Ethan

Here's what I have:

*   def predict\_bitcoin\_price(today\_price):
*       '''
*       Predict bitcoin prices over time.

*       Parameters
*       ----------
*       today\_price : float|int
*           Price today.

*       Returns
*       -------
*       today\_price : float|int
*           Price today.
*       five\_years : float|int
*           Price in five years.
*       ten\_years : float|int
*           Price in ten years.
*       '''
*       five\_years = today\_price / 2
*       ten\_years = 0
*       return today\_price, five\_years, ten\_years

Yay!