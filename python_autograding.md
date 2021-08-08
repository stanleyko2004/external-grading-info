# How to use the Python Autograder for External Grading
This is the next step after the [External Grading setup guide](https://github.com/ace-lab/pl-ecc-csci7/wiki/External-Grading). 

**Note**: This guide is only to be used to create basic External Grading Python questions. Not meant to replace the [official documentation](https://prairielearn.readthedocs.io/en/latest/python-grader/)

**Double Note**: At any point in this guide, if you feel lost or just need some visualization to get a firm grasp of things. My first ever External Question: [fillinRobotInGrid question](https://github.com/ace-lab/pl-ecc-csci7/tree/Knguyen/questions/python/fillinRobotInGrid) should give you an example of what things would look like. I included many comments to help you on your journey so I hope you will stay till the end 🥺 . 

## Purposes
- When grading the student's Python code, PrairieLearn's Python Autograder will be needed. It will take quite a while to set it up and even more just to have Python Autograder grades your questions correctly 😩 .
- The docs for Python Autograder offer everything. However, most of them are quite extensive and will most likely not be used in this guide. For any information that this guide doesn't have, you can visit the [Python Autograder PrarieLearn documentation page](https://prairielearn.readthedocs.io/en/latest/python-grader/).

## Background
Creating an External Question, especially a complicated one will easily double your workload for that question. Not only that you will have to write and test out your question like you normally do, with Python external questions you will have to worry about tests cases and feedbacks given to students' code to say the least. 

**Therefore, make sure that once you decide to create a Python External Grading question, you seriously want to do it 💯.**

On the good side, I think if you mastered using the Python Autograder, you can boast that you're able to make questions that pl-ucb-csci10 couldn't have made 😂 (I tried to find some External Questions when I first met this obstacle in their repo, I couldn't find any example questions that utilize the Python Autograder). You will also be able to use all the question elements available in PrairieLearn to your liking.

With that out of the way, let's get cracking 🧠.

## Prerequisite
Make sure that your info.json is properly set up for the language you planned to use (this step should be good if you follow my guide on [setting up info.json for External Questions for Python](https://github.com/ace-lab/pl-ecc-csci7/wiki/External-Grading#python))

## Getting Started
### Overview
![Flow Chart for Designing Question for Python Autograder](https://prairielearn.readthedocs.io/en/latest/python-grader/grader-structure.png)

This is a **SUPER USEFUL CHART** that will help greatly in figuring out the flow of creating tests code running against the students' Python code. It lays out how data will be separated, which will be allowed for the students to use, and which will be used in our tests.

**WARNING**: The data in the chart is **NOT INHERITABLE**. What I mean is that the data `names_for_user` will **NOT** be available to the `test.py` at the final stage. **ONLY** `names_from_user` will be accessible in `test.py` 🧐 

### tests/
First, you will need to create a folder called `tests` inside your question folder. The folder hierarchy will closely resemble the following:
```
/course             # the root directory of your course
+-- /questions           # all questions for the course
|   `-- /addVector
|       +-- info.json           # required configuration goes here (see below)
|       |-- ...                 # some other question files
|       `-- /tests              # folder of test cases
|           +-- ans.py           # server answer
|            -- setup_code.py    # set up code
|            -- test.py          # testing code
|           |-- ...         # Some extra files
```
The folder structure above is an example of the External Grading question "addVector". If you want a more explicit view of the folder structure, check out my 👉 [fillinRobotInGrid question](https://github.com/ace-lab/pl-ecc-csci7/tree/Knguyen/questions/python/fillinRobotInGrid) (totally not a plug 👀 )
#### Requirements:
There are **at least** 3 files that you will always need exist in your `/tests` folder for Python Autograding to work:
|Files |Description |
|------|:----------:|
|ans.py| Contains the "example correct answer" that the question creator will be used to test the student's answer against.|
|setup_code.py| The most important file above all. This Python file will be run right after the `server.py`. Every data created in this file will be either copied or exist all the way until the `test.py` is done running.|
|test.py| This will run after everything is done processing. It will test the student-submitted codes against test cases created by the question author.|

## The Real Deal - Default Files
At this point, there's no turning back. I expect you to already have the question panel to display and you have a good idea of what you want the student to type in the code window (or code submission). If you have reached this point without being swayed from pursuing the External Grading path yet, pat yourself on the back 👏. Afterward, get some coffee ☕ or water because we going to dive in deep.

### `question.html` 📋 
This is a file outside of the `tests/` folder, it's something that you should have already to get your question to display properly. I won't be talking about the `<pl-question-panel>` part because it will widely vary between questions and doesn't do anything with the External Grading process. What's important it's any `<pl-xxx>` element that has an external grading option for them.

Since there is a lot of `pl-element`, I will be mainly focused on `<pl-file-editor>` since I think that will be what most people use to grade students' codes 🤔. For other `pl-element`, I sincerely apologize but you will have to dig into them yourself in the [documenation page](https://prairielearn.readthedocs.io/en/latest/elements/). Just find any element that allows external grading as an option.

An example of what could be:
```html
<pl-question-panel>
   ...  <!-- Some extra contents for questions -->
  <pl-file-editor file-name="user_code.py"></pl-file-editor>     <!-- Create a coding card for the student to code inside -->
</pl-question-panel>

<pl-submission-panel>
  <pl-external-grader-results></pl-external-grader-results>     <!-- Display the result after grading the student code -->
  <pl-file-preview></pl-file-preview>       <!-- Display the submitted student code -->
</pl-submission-panel>
```
- The option `file-name="user_code.py"` determines the *title of Bootstrap coding card*. It represents the name of the python file where the student's code will be stored inside. You can change it **BUT** ⚠️ you must also [add another variable in `test.py`](#change-the-file-name-option-in-questionhtml)
- There are more options for the `<pl-file-editor>` in the [docs](https://prairielearn.readthedocs.io/en/latest/elements/#pl-file-editor-element) that allow you to change the card background color, syntax highlighting, and more that could potentially help your students.

#### **NOTE**: You can also put lines of code between the `<pl-file-editor> {{code_here}} </pl-file-editor>` to have them display code inside the card beforehand.

**Example**:
```html
<pl-file-editor file-name="fib.py" ace-mode="ace/mode/python" ace-theme="ace/theme/monokai">
# Sample starter code
def fib(n):
    pass
</pl-file-editor>
```
![example of <pl-file-editor>](https://prairielearn.readthedocs.io/en/latest/elements/pl-file-editor.png)


OK! We have passed the first part, let's move onto another level 👍 


***

### `server.py` ⚙️ 
This will be the last default file we touch on before moving toward the new files needed in the `tests/` folder.

**NOTE:** Throughout the course of creating and fixing our tests, we may have to revisit `server.py` a lot of times so have it open on the side would be a wise choice.

Works involving with the `server.py` will be either extremely easy or extremely exhausting 😔 depends on how good a job you did in figuring out two things:
|data['params']| Description|
|--------------|:-----------:|
|names_for_user| Data that will be given to the students to use|
|names_from_user| Data that will be needed from the students and available in `test.py`|

If you look at the flow chart at the beginning of the guide, you can see that the student will be expected to use `names_for_user` to produce `names_from_user`.
#### WARNING: *ONCE AGAIN* The data in the chart is **NOT INHERITABLE**. This forces you to plan what data you should test and what data you should give 😨. This problem caused me many days and should be something that you keep in mind moving forward.

- #### Data for students (`names_for_user`) - Only accessible in student's code
    These are variables, functions, or any objects that the students could assign, call or do whatever they want in their coding environment. The reason for this is to provide the students with predefined functions or variables for them to work with.

    To define data for the students, follow the template below:
```python
def generate(data):
    data["params"]["names_for_user"] = [  #This will allow the student to access the two float variable x and y
        {"name": "x", "description": "Description of the variable", "type": "float"},
        {"name": "y", "description": "Description of the 2nd variable", "type": "float"},
        # ... Add more if you want
    ]
```
 
Don't include too much data for the student to access, only just enough so that they cannot pull any fast one 😈 
**NOTE:** Be warned that if the object you allow the student to access requires any extra data (e.g. a global variable to read), then you also need to include said data into the `names_for_user` list.

- #### Data from students (`names_from_user`) - Only accessible in `test.py`
    These could be variables, functions, or any objects that will be used for test cases in `test.py`. This could be used to specify for the students what is expected of them. **WE CAN ONLY ACCESS `names_from_user` DATA FOR OUR TEST** so make sure that you know beforehand what kind of data you will need for all your tests cases.

    The template to define data needed for tests cases are the same as `names_for_user`:
```python
def generate(data):
    data["params"]["names_from_user"] = [    # Now we will be able to use the "fib" function in our test.py file
        {"name": "fib", "description": "Fibonnaci function", "type": "Python int function"},   #The student have to create a "fib" function in their code
   
        # ... Add more if you want
    ]
```

#### In both dictionaries, each of the entries needs to have the following keys and values pair:

|Key|Value Meaning|
|---|:---:|
|"name"| The name of the object the student can use, **THIS NEED TO BE ACCURATE**|
|"description"| The description of the variable, can be just left blank if you don't want to type out the whole thing|
|"type"| The data type of the object, doesn't need to be exactly correct|

##### <u>Extra</u>: There's a PrairieLearn's HTML Element called [`pl-external-grader-variables`](https://prairielearn.readthedocs.io/en/latest/elements/#pl-external-grader-variables-element) you can used to display the `names_for_user` and/or `names_from_user` onto the question panel. It will display a table with all the "names", "description" and "type" values for the students. However, I **highly recommend** avoiding using these features unless you're sure that the students won't be able to take advantage of knowing all these data.

That marked the final steps of what we needed to do that involve our default files 😌. Now that we have declared which data is available to students and which is available to our tests, we will now move on to CREATE THOSE DATA 🤩 

## The Real Deal - `tests/` Files
This is where you will create the foundation for your tests. Once again I am **highly advise** you to have another tab open with just the [flowchart at the beginning of the guide](https://github.com/ace-lab/pl-ecc-csci7/wiki/Python-Autograding/#overview). It will be immensely helpful throughout our times here 🥛 

### `tests/setup_code.py` 🖥️ 
This file will be run ONCE before the student's answer and the reference answer (`ans.py`). Here you will create those objects that will be available for students to use.

⭐ **<u>IMPORTANT POINT:</u>** Different from what you may perceive of the flowchart, the data from `setup_code.py` is actually split into two **COPIES**. In the first copy, Any data that are in the dictionary `data["params"]["names_for_user"]` will be given to the student's code. For the other copy, **ALL** of its' data will be given to the reference answer (`ans.py`)

**NOTE:** Only `setup_code.py` and `test.py` have access to the `data` dictionary from `server.py`. If you want to use any values in the `data` dictionary either in the students' code or any other files in the `tests/` folder, you have to assign the value in your `data` dictionary to a variable.

For example:
- In `server.py`:
```python
import random

def generate(data):
    data["params"]["names_for_students"] = [
        {"name": "start", "description": "The start point of something", "type": "python integer"},    # Declare "start" as a variable the student can access
    ]
    data["params"]["names_from_user"] = [
        {"name": "current", "description": "Student current position", "type": "Python integer"},    # Declarer "current" as a variable we can access in test.py
    ]

    data["params"]["coordinate"] = random.randInt(1,9)    # a randomized starting coordination
    data["params"]["correct"] = random.choice(True, False)
    data["params"]["ending"] = random.randInt(10, 19)
    ...   # More code below
```
- In `setup_code.py`:
```python
# Data that we want the students to use
start = data["params"]["coordinate"]    # We want the student to use a non-empty variable. It's also a way to give the student a randomized variable
current = start

# Data for internal uses
internal_correctness = data["params"]["correct"]    # Any data that is not in the dictionary "names_for_students" is internal
server_end = data["params"]["ending"]

... # More code below
```

***

### `tests/ans.py` 🥇 
This file will act as an "*example answer*" from the question you given to the student. **IT'S PERFECTLY OK TO HAVE THIS FILE BE EMPTY IF YOU DON'T WANT TO** 😄. In many cases, you will test your "reference answer" against the student's answer in `test.py`. But it's also totally valid to test the student's answer using something else.

`ans.py` gets **EVERY** object (variables, functions, etc) from `setup_code.py`, including any data that is given to the students. Therefore, you can make a quick **perfect answer** to test against your student's submitted answer 😮. I'll let you think why that would be the case.

###### I haven't tested not including `ans.py` inside the `tests/` folder but I usually still left it in just in case there's any side effect.

Example for a Fibonacci function question
- In `ans.py`:
```python
def fib(n):      # The student's Fibonacci function will be tested against this function in test.py.
    if n <= 1:
        return 1
    else:
        return fib(n - 1) + fib(n - 2)    # if the student's function result the same as the result of ans.py function, then it counts as correct answer
```

***

### `tests/test.py` 🚨 
Here it is, one of the most confusing and frustrating parts of our Python Autograding journey, the final stage, and probably where you will keep returning to find errors. Your last struggle ✊ 

Here is where you will grade the students and report feedbacks for the students or debugging purposes. To make a `test.py`, you first need to know about the `self` object:
|Name| Description|
|----|:----------:|
|`self.st`| Use to access the student's answer objects (aka the objects you need for your tests cases in `data["params"]["names_from_user"]`)|
|`self.ref`| Use to access the reference answer objects (aka every object that defined in `setup_code.py` because reference answer gets all data.  I like to call them *server objects*|
|`self.data`| Use to access the `data` dictionary from `server.py`. Only `setup_code.py` and `test.py` can access the `data` dictionary. |

**REMEMBER:** The objects from the student's answers and the objects from the reference answers are two COPIES like mentioned before (e.g. don't expect a function called by the students to affect the reference answer's objects and vice versa)

I **recommend** to follow the default code template that PrairieLearn has for their `test.py`. However, if you want to know more about what each line does, I suggest you give a more detailed look into the [`tests/test.py` section in PrarieLearn docs](https://prairielearn.readthedocs.io/en/latest/python-grader/)

- Template:
```python
from pl_helpers import name, points, not_repeated
from pl_unit_test import PLTestCaseWithPlot, PLTestCase
from code_feedback import Feedback
from functools import wraps
import numpy as np     #* You might not need numpy for your tests, but I still included it in case any other library requires numpy
import numpy.random

# No Plot Grading - You don't use the <pl-drawing> with an external grading option
class Test(PLTestCase):
    @points(1)    # Maximum points that this test case will give if passed
    @name("Check return value")     # Name of the test case for reporting in case something goes wrong
    def test_0(self):
        ...       # Contents of the test case

    def test_1(self):       # Name your test with an increasing numerical value (test_xx) is highly recommend to ensure the testing order
        ...


# Yes Plot Grading
# class Test(PLTestCaseWithPlot):     # Substitute with the other class if your question use Plot
```
###### No need to worry if your compiler yells at you for `import` non-existence library. Those libraries will be included automatically by PrairieLearn when run. One sad thing about it is that we won't be able to use Intellisense to see each function the library offers, maybe in the future I will be able to find out there's a way we can have that 😅.
The template uses classes and decorators (@) in Python. If you want to learn more you can look into the docs about it. The template I provided should work for your case 99% of the time though I hope 😃 

- The `Feedback` library contains many Python functions that you should use to test the student's answers. I will not be able to list all of them, you should totally check the [Code Feedback section in the Python Grader docs](https://prairielearn.readthedocs.io/en/latest/python-grader/sphinx-docs/) to learn about all of them.

In the following example, I will use `Feedback.check_scalar()` function.
#### An example of how a typical `test.py` will look:
```python
from pl_helpers import name, points, not_repeated
from pl_unit_test import PLTestCaseWithPlot, PLTestCase
from code_feedback import Feedback
from functools import wraps
import numpy as np
import numpy.random


class Test(PLTestCase):
    @points(1)
    @name("Compare student location to the reference answer destination")
    def test_0(self):
        server_destination = [self.ref.server_end]      # Get the 'server_end' variable from the server side (reference answer's copy of setup_code.py data)
        student_location = [self.st.current]       # Get the 'current' variable from the student side (student answer's copy of setup_code.pyt data)

        ## check_scalar(name, ref, data, rtol=1e-5, atol=1e-8) Checks that a scalar value is close to the reference solution using specified rtol and atol.
        '''
        "name": The name of the object being compared. This will be printed in the <pl-submission-panel>. Tips: You can use python string substitution to access the "ref" and "data" variables while debugging the question
        "ref": The reference answer's object to compare to the student. Make sure you don't swap the position of the arguments of "ref" and "data". 
        "data": The student answer's object to compare. It will be under strict checking, in this case, the "data" will need to be a scalar value for the test to NOT report errors back to the students.

        The function will return true if the "ref" and "data" are the same, false if not. It will also provide feedback to the students if they have syntaxes errors.
        '''

        if Feedback.check_scalar("Location of the student", server_destination, student_location):
            Feedback.set_score(1)     # Use function set_score(n) to award student points. "n" can only be equal to "@points(n)" max.
        else:
            Feedback.set_score(0)

```
- It takes practice to get used to using different `Feedback` functions to get what you want, but I trust you will be able to pull through the finish line. If this is your first time, I **HIGHLY RECOMMEND** to just make a simple test case to test out your `test.py` first, something like:

```python
from pl_helpers import name, points, not_repeated
from pl_unit_test import PLTestCaseWithPlot, PLTestCase
from code_feedback import Feedback
from functools import wraps
import numpy as np
import numpy.random


class Test(PLTestCase):
    @points(1)
    @name("Compare student location to the reference answer destination")
    def test_0(self):
        Feedback.set_score(1)
```

***
## Conclusion
**YOU DID IT** 🥳 🥳 🥳 🎉, you now know everything that I can say about PrairieLearn Python External Grading questions. It was a long and challenging journey but now you have been armed with another great tool to create questions for students.

To encourage more participation in the External Grading rabbit hole, I will create and maintain a list of your External Question (if you want to put it here) 😉. Thank you for your time with me. If there's any External Grading problem or questions you want me to look at, please feel free to contact me on Slack (or just email me using my GitHub emails 👀 )


## Extra
### Leading and Trailing Code - `tests/leading_code.py` and `tests/trailing_code.py`
###### Thanks Audrey Lin for mentioning this
There are two optional files you can add inside the `tests/` folder:
| Name | Description |
|------|:-----------:|
|leading_code.py| The code in this file will be run **before** setup_code.py. It will be appended **ABOVE (or before)** the student's code before grading inside test.py |
|trailing_code.py| The code in this file will be run **after** setup_code.py. It will be appended **BELOW (or after)** the student's code before grading inside test.py |

💭 Using the above two files, you can modify the behavior of the student's code. Example: The usage of the `leading_code.py` in my [Robot Question](https://github.com/ace-lab/pl-ecc-csci7/blob/1c9a802bcf24b072017f1aa4e8ff04d8442e0812/questions/python/fillinRobotInGrid/tests/leading_code.py)
- `tests/leading_code.py`
```python
... # More code above

def RightXTimes(x):
    """ Move the arrow to the right x numbers of times
    Args:
        x: The number of times it will turn right in Int
    Returns:
        The angle representation of direction in Int
    """
    global cur_direc
    cur_direc = turn_right(x, cur_direc)


def MoveXTimes(x) -> int:
    """ Edit the new x or y location when moving the arrow forward
    
    Args:
        x: The number of times you want to move forward
    """
    for i in range(0, x):
        try:
            if cur_direc == 0:  #X-direction, to the right
                cur_local[0] = moveTo_local(board_x, cur_local[0], one_sqr_px)
            elif cur_direc == 180: #X-direction, to the left
                cur_local[0] = moveTo_local(board_x, cur_local[0], -one_sqr_px)
            elif cur_direc == 90:   #Y-direction, down
                cur_local[1] = moveTo_local(board_y, cur_local[1], one_sqr_px)
            elif cur_direc == 270:   #Y-direction, up
                cur_local[1] = moveTo_local(board_y, cur_local[1], -one_sqr_px)
        except:
            raise ValueError("Moved outside the board")
```
☝️ I created those two functions inside `leading_code.py`. As a result, those functions definitions will be appended on top of the student's code before grading. Therefore, when we run our test cases inside `test.py`, PrairieLearn won't complain about not having function `RightXTimes` and `MoveXTimes` defined.

I also put RightXTimes and MoveXTimes into `names_for_user` so the student can access those functions in their code. **THIS STEP IS REQUIRED**

~~I found the Leading and Trailing code to be niche and only useful in the case I had. If anyone feels that there are other uses for Trailing code and Leading code please do tell me 👍~~

#### Additional Python packages usage 🥳 (thanks to Audrey Lin for the info)
Another use for `leading_code.py` is to import custom python packages that you want the student to use. 

**AT THE TIME OF WRITING THE WIKI**, these packages are already included in the PrairieLearn and do NOT need to do any extra steps to use them:
- `PrairieLearn/images/plbase/python-requirements.txt`:
```
chevron==0.14.0
flake8==3.9.2
flake8-quotes==3.2.0
lxml==4.6.3
matplotlib==3.4.2
networkx==2.6.2
numpy==1.21.1
pandas==1.3.1
pycryptodome==3.10.1
pyquaternion==0.9.9
regex==2021.7.6
scipy==1.7.0
sympy==1.8
rpy2==3.4.5
pygraphviz==1.6
ansi2html==1.6.0
scikit-learn==0.24.2
nltk==3.6.2
tzlocal==2.1
Pygments==2.9.0
sphinx==4.1.2
sphinx-markdown-builder==0.5.4
recommonmark==0.7.1
mkdocs-material==7.2.1
PuLP==2.4
text-unidecode==1.3
statsmodels==0.12.2
openpyxl==3.0.7
```
⚠️ **BEFORE YOU DO ANY EXTRA STEPS TO GET THE PACKAGE YOU NEED INTO PRAIRIELEARN**, check out the [latest list of packages inside PrairieLearn](https://github.com/PrairieLearn/PrairieLearn/blob/master/images/plbase/python-requirements.txt)

If you want to get additional packages into Python Autograding (e.g. datascience package), you must use these commands when starting up PrairieLearn
- In your terminal:
```bash
<normal commands to run docker with external grading>
pip3 install --target /course/questions/<path to your question folder>/tests/ <library name>
```
Afterward, you can use `leading_code.py` to 'import' important packages that you want the student to use in their answer.
- In `leading_code.py`:
```py
from datascience import *
import numpy as np

# More code down below
```


### Change the `file-name` option in `question.html`
###### Credit to Audrey Lin and Tyler Chen to give me more information about `question.html` 💯 
When you change the file name in the `<pl-file-editor> option. You also need to add a variable called `student_code_file` in the *Test* class inside the `test.py` file.

- As an example, here's [Tyler Chen's question](https://github.com/ace-lab/pl-ecc-csci8/tree/4df13d3a781cab33471f3bf62fd4a19a401f45b0/questions/Modules1-5/FinalExamQ19/FinalExamQ19.3):
```html
<!-- More code above -->

<pl-file-editor file-name="compute_letter_grades.py" ace-mode="ace/mode/python">   <!-- Tyler uses "compute_letter_grades.py" instead of the default "user_code.py" file name -->
def compute_letter_grades(avg_score):
    ...
</pl-file-editor>

<!-- More code below -->
```

🔢 Here's what you have to add in `test.py`
- Tyler Chen's `test.py`
```python
from pl_helpers import name, points, not_repeated
from pl_unit_test import PLTestCaseWithPlot, PLTestCase
from code_feedback import Feedback
from functools import wraps
import numpy as np
import numpy.random
import random

class Test(PLTestCase):

    student_code_file = "compute_letter_grades.py"    # You have to add the variable "student_code_file" and assign with the name
                                                    # you given.
    @points(1)
    @name("Test case")
    def test_0(self):

# More code down below
```
