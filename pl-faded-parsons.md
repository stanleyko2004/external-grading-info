# `pl-faded-parsons` element

Create and display modified parsons elements (with blanks and distraction answers).

#### Sample element

```html
<pl-faded-parsons>def add(a, b): #0given
return !BLANK

</pl-faded-parsons>
```

#### Customizations

Attribute | Type | Default | Description
--- | --- | --- | ---
`file-name` | string | "user_code.py" | What name to save the student answer as. This will be sent to the autograder.

#### Details

Special Syntax | Description
--- | ---
`!BLANK` | When rendered, this will show as a blank for the student to fill in.
`#0given` | When rendered, this will line of code will have **0** indents and will be **given** to the student (put in the solution panel). If no **#** is provided, that line of code will not be placed in the solution panel

#### Example implementations

- [adding numbers (very simple)](https://github.com/ace-lab/pl-ucb-faded-parsons/tree/grading/questions/adding)
- [compute_poly (more complicated)](https://github.com/ace-lab/pl-ucb-faded-parsons/blob/master/questions/compute_poly)

#### See also

- [`pl-question-panel` for displaying the question prompt.](https://github.com/PrairieLearn/PrairieLearn/blob/master/docs/elements.md#pl-question-panel-element)
- [`pl-submission-panel` for changing how a submitted answer is displayed.](https://github.com/PrairieLearn/PrairieLearn/blob/master/docs/elements.md#pl-submission-panel-element)
- [`pl-answer-panel` for displaying the question's solution.](https://github.com/PrairieLearn/PrairieLearn/blob/master/docs/elements.md#pl-answer-panel-element)
- [`pl-external-grader-results` for showing the results from an externally graded code question.](https://github.com/PrairieLearn/PrairieLearn/blob/master/docs/elements.md#pl-external-grader-results-element)
- [more details on setting up pl-faded-parsons element](https://github.com/stanleyko2004/external-grading-info/blob/master/pl-faded-parsons-detailed-guide.md)