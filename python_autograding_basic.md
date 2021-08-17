# Basic Information for Python Autograding
This is the information you'll need for the most basic python autograding. I will be going over how to make and grade an adding question (example [here](examples/adding)).

## Overall Course Directory
This is what the overall course Directory should look like.
```
/course             # the root directory of your course
+-- /questions           # all questions for the course
|   `-- /adding
|       +-- info.json           # required configuration goes here (see below)
|       +-- question.html       # question file with HTML markdown
|       +-- server.py           # names_for_user and names_from_user
|       +-- /tests              # folder for testing
|       |   +-- ans.py           # server answer
|       |   +-- setup_code.py    # set up code
|       |   +-- test.py          # testing code
```

We'll go through the each file indivdually.

### `info.json`
This is the config file for the question.
```
{
  "uuid": "be987220-ca54-11eb-a3a2-acde48001123",
  "title": "Adding Faded Parsons Example",
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

The keys you need to add are listed below.

Key | Type | Description
--- | --- | ---
`gradingMethod` | string | tells PrairieLearn that this question will be graded externally
`externalGradingOptions` | JSON object | configuration for external grading
`enabled` | boolean | set external grading to true
`image` | string | which image to be used (docker image if running locally)
`entrypoimt` | string | where to start executing code (`/python_autograder/run.sh` usually gets the job done)

### `question.html`
This is the html markdown that tells PrairieLearn what to render
```html
<pl-question-panel>
  Write a python function that returns the sum of 2 numbers<br><br>

</pl-question-panel>

<pl-file-editor file-name="user_code.py">def add(a, b):
</pl-file-editor>

<pl-external-grader-results></pl-external-grader-results>
```
The `<pl-question-panel>` is the question that will be shown to the student.

The `<pl-file-editor>` is where the student edits code. `file-name="user_code.py"` is what file name PrairieLearn will send over to the autograder (`user_code.py` is default).

The `<pl-external-grader-results>` is optional but is extremely helpful. It shows grader feedback and score to the student.

### `server.py`
This is where you define what variables you give and receive from the student.
```python
def generate(data):

    # Define the variables here
    names_for_user = []
    names_from_user = [
        {"name": "add", "description": "python function that returns sum of 2 numbers", "type": "python function"}
    ]

    data["params"]["names_for_user"] = names_for_user
    data["params"]["names_from_user"] = names_from_user

    return data
```
1. `names_for_user` is a list of dictionaries describing variables for the student to use. In this case, adding is just a function so this is not required.
2. `names_from_user` is a list of dictionaries describing variables that the grader should pull from the student code. In this case, we want to pull the `add` function from the student code so we can test it.

##### Note: `name` must be exactly the variable you expect to see in the student code. Description and type don't really matter.

### `test/`
Folder for the autograding test files.

### `tests/ans.py`
The answer to your question.
```python
def add(a, b):
    return a+b
```
You will be able to reference this in `tests/test.py`

### `tests/setup_code.py`
Code to set up some variables. Here is where we use `data['params']['names_for_user']`. Because we did not define any variables for the user to use, we can just leave this file blank.

### `tests/test.py`
Here is where we can our tests.
```python
from pl_helpers import name, points
from pl_unit_test import PLTestCase
from code_feedback import Feedback

class Test(PLTestCase):
    @points(1)
    @name("test 1")
    def test_0(self):
        case = [1, 2]
        user_val = Feedback.call_user(self.st.add, *case)
        ref_val = self.ref.add(*case)
        points = 0
        if Feedback.check_scalar(f'case: {case}', ref_val, user_val):
            points += 1
        Feedback.set_score(points)
```
- PrairieLearn comes with many features. The 3 imports at the beginning are 3 of the most important features.

- In class `Test`, that extends `PLTestCase`, you will have your test cases.

- `@points` decorator signifies how many points the test case will be worth.

- `@name` decorator is just what the test case will be called

- `test_0` is the function name. Make sure the test case starts with `test_` or else PrairieLearn will not see that function as a test case.

- `self` is where a lot of data is stored. Details below.

|Name| Description|
|----|:----------:|
|`self.st`| Student's answer objects (variables or functions you defined in `data["params"]["names_from_user"]`)|
|`self.ref`| Reference answer object. Can get any variable or function.|
|`self.data`| Use to access the `data` dictionary from `server.py`. Only `setup_code.py` and `test.py` can access the `data` dictionary. |

- `Feedback.call_user` takes a function to call and arguments to pass into said function. `*case` is special python syntax for unpacking arguments.

-  `Feedback.check_scalar` takes a name, a reference number (integer, float, etc), and a student number. It will compare them and give feedback that will show up in the feedback panel in `<pl-external-grader-results>`.

- `Feedback.set_score` takes a float that will be how much of the test case the student got right. For example, if there were 5 cases and the student got 1 correct answer, you would pass in 0.2