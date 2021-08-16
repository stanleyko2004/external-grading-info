## Overall Course Directory
This is what the overall course Directory should look like.
```
/course             # the root directory of your course
+-- /questions           # all questions for the course
|   `-- /question1
|       +-- info.json           # required configuration goes here (see below)
|       +-- question.html       # question file with HTML markdown
|       +-- server.py           # names_for_user and names_from_user
|       +-- /tests              # folder for testing
|       |   +-- ans.py           # server answer
|       |   +-- setup_code.py    # set up code
|       |   +-- test.py          # testing code
|       |   `-- ...         # Some extra files
|       `-- /serverFilesQuestion # some utility files
|           `-- code_lines.py    # Code lines that will be shown in pl-faded-parsons element
```

## `info.json`
Make sure that your `info.json` is properly set up.

Add the following lines:
```
"gradingMethod": "External",
"externalGradingOptions": {
  "enabled": true,
  "image": "prairielearn/grader-python",
  "entrypoint": "/python_autograder/run.sh"
}
```
A complete `info.json` should look like this:
```
{
  "uuid": "be987220-ca54-11eb-a3a2-acde48001122",
  "title": "Faded Parsons example 1",
  "topic": "",
  "tags": ["berkeley", "fp"],
  "type": "v3",
  "gradingMethod": "External",
  "externalGradingOptions": {
    "enabled": true,
    "image": "prairielearn/grader-python",
    "entrypoint": "/python_autograder/run.sh"
  }
}
```
For other options such as setting a time limit or enabling internet, check the [PraireLearn Documentation for `info.json`](https://prairielearn.readthedocs.io/en/latest/externalGrading/#configuring-and-enabling-external-grader-support)

### `question.html`
This is where the HTML for the question will be.
Use the following element and attributes for a python graded faded parsons element.
```
<pl-faded-parsons language="py" partial-credit="true" line-order="alpha"></pl-faded-parsons>
```

![Flow Chart for Designing Question for Python Autograder](https://prairielearn.readthedocs.io/en/latest/python-grader/grader-structure.png)

##### Chart that will be referenced in the future


### `server.py`
|`data['params']`| Description|
|--------------|:-----------:|
|`names_for_user`| Data that will be given to the students to use (might be empty if student's code runs independently)|
|`names_from_user`| Data that will be retrieved from the students and available in `test.py`|

If you look at the flow chart at the beginning of the guide, you can see that the student will be expected to use `names_for_user` to produce `names_from_user`.

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
|`"name"`| The name of the object the student can use, **THIS NEED TO BE ACCURATE**|
|`"description"`| The description of the variable, this doesn't really matter (can be left blank)|
|`"type"`| The data type of the object, this doesn't really matter (can be left blank)|

### `tests/`
There are 3 files that you will always need exist in your `/tests` folder for Python Autograding to work:
|Files |Description |
|------|:----------:|
|`ans.py`| Contains the correct answer. This code will also be presented to the student on submission|
|`setup_code.py`| This Python file will be run right after the `server.py`. Every data created in this file will be either copied or exist all the way until the `test.py` is done running.|
|`test.py`| This will run after everything is done processing. It will test the student-submitted codes against test cases created by the question author.|

### `tests/setup_code.py`
This file will be run ONCE before the student's answer and the reference answer (`ans.py`). Here you will create those objects that will be available for students to use.

In `setup_code.py` you will define variables for the user to use. If the student's code can run independently (no global variables or local variables), this file can be left empty.
*However, you cannot omit this file*

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

# ... More code below
```

***

### `tests/ans.py`
This file will act as an "*example answer*" from the question you give to the student.

The code in this file will also be shown in the submission panel, so comments will be helpful.

Example for a computer_poly function question
- In `ans.py`:
```python
def poly(coeffs, x):
    # Keep track of the total as we iterate through each term.
    # Each term is of the form coeff*(x**power).
    total = 0
    # Extract the power and coefficient for each term.
    for power, coeff in enumerate(coeffs):
        # Add the value of the term to the total.
        total = total + coeff * (x ** power)
    return total

```

***

### `tests/test.py`

Here is where you will grade the students and report feedback for the students or debugging purposes. To make a `test.py`, you first need to know about the `self` object:
|Name| Description|
|----|:----------:|
|`self.st`| Use to access the student's answer objects (aka the objects you need for your tests cases in `data["params"]["names_from_user"]`)|
|`self.ref`| Use to access the reference answer objects (aka every object that defined in `setup_code.py` because reference answer gets all data.  I like to call them *server objects*|
|`self.data`| Use to access the `data` dictionary from `server.py`. Only `setup_code.py` and `test.py` can access the `data` dictionary. |

PL has some built in functions that make it easy to report feedback. For a more detailed look, look at the [code feedback](https://prairielearn.readthedocs.io/en/latest/python-grader/) section of the PL python grader docs.

Create a test class that extends PLTestCase and inside, you can have testcases that begin with test_ (as shown).

Here is a basic `test.py` file that will work

```python
from pl_helpers import name, points
from pl_unit_test import PLTestCase
from code_feedback import Feedback

class Test(PLTestCase):
    @points(1)
    @name("Test example")
    def test_0(self):
        Feedback.set_score(1)
```

This example will just give a score of 1 (100%) regardless of what the student submits.

- The `Feedback` library contains many Python functions that you should use to test the student's answers. I will not be able to list all of them, you should check the [Code Feedback section in the Python Grader docs](https://prairielearn.readthedocs.io/en/latest/python-grader/sphinx-docs/) to learn about all of them.

- Here is an example of a functioning test.py function for computing a polynomial. It stores the user and reference values in variables and uses Feedback.check_scalar to compare them.

```python
from pl_helpers import name, points
from pl_unit_test import PLTestCase
from code_feedback import Feedback


class Test(PLTestCase):

    @points(1)
    @name("testing single case")
    def test_0(self):
        case = [[10], 3]
        points = 0
        user_val = Feedback.call_user(self.st.poly, *case)
        ref_val = self.ref.poly(*case)
        if Feedback.check_scalar(f"args: {case}", ref_val, user_val):
            points += 1
        Feedback.set_score(points)

```

***


## Extra
### Leading and Trailing Code - `tests/leading_code.py` and `tests/trailing_code.py`
There are two optional files you can add inside the `tests/` folder:
| Name | Description |
|------|:-----------:|
|`leading_code.py`| The code in this file will be run **before** `setup_code.py`. It will be appended **ABOVE (or before)** the student's code before grading inside test.py |
|`trailing_code.py`| The code in this file will be run **after** `setup_code.py`. It will be appended **BELOW (or after)** the student's code before grading inside test.py |

Using the above two files, you can modify the behavior of the student's code. You could put utility functions here for the student to use. Example:
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
I created those two functions inside `leading_code.py`. As a result, those functions definitions will be appended on top of the student's code before grading. Therefore, when we run our test cases inside `test.py`, PrairieLearn won't complain about not having function `RightXTimes` and `MoveXTimes` defined.

I also put RightXTimes and MoveXTimes into `names_for_user` so the student can access those functions in their code. **THIS STEP IS REQUIRED**

#### Additional Python packages usage ?? (thanks to Audrey Lin for the info)
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
**BEFORE YOU DO ANY EXTRA STEPS TO GET THE PACKAGE YOU NEED INTO PRAIRIELEARN**, check out the [latest list of packages inside PrairieLearn](https://github.com/PrairieLearn/PrairieLearn/blob/master/images/plbase/python-requirements.txt)

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