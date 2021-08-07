# External Grading - TLDR version ?? 
### Here I will explain to the best of my understanding how External Grading works, especially the one using Python Autograder. 
(Of course to understanding things fully, always look into PrairieLearn's External Grading [main site](https://prairielearn.readthedocs.io/en/latest/externalGrading/))

## Purposes
- Any time that you use a question that requires the student to create some kind of code sequences (ex: [&lt;pl-file-editor&gt;](https://prairielearn.readthedocs.io/en/latest/elements/#pl-file-editor-element)), you will have to use either PrairieLearn's Grader or grade it manually after the student submission. I will not discuss the Manual Grading method and solely focus on External Grading only.
- I feel like the documentation page for External Grading is very bulky for what I actually used. I think it would be much better to get External Grading to run before dive into the document's pages.
- Because I like writing wiki pages sometimes, even though it's not my strong suit ?? 

## Background
In the actual Deployment scenario, PrairieLearn's External Grader is actually hosted on AWS services (for security and performance reasons ?? ). Some of the steps below might be different when comparing local development and deployment to students. All the steps below are strictly views under the assumption that we running these External Grading *locally*. 

**DO NOT USE THEM AS-IS WHEN DEPLOYING TO STUDENTS.**

## Getting Started
- First step, you going to need to use different commands to start Docker, one that will enable running External Grading locally:

#### For macOS, navigated first to your `pl-ecc-csci7` (or `pl-ecc-csci8` folder):
```sh
docker run -it --rm -p 3000:3000 \
    -v "$PWD":/course `# Map your current directory in as course content` \
    -v "$HOME/pl_ag_jobs:/jobs" `# Map jobs directory into /jobs` \
    -e HOST_JOBS_DIR="$HOME/pl_ag_jobs" \
    -v /var/run/docker.sock:/var/run/docker.sock `# Mount docker into itself so container can spawn others` \
    prairielearn/prairielearn
```

#### Using Window, open *PowerShell* at your work folder, then do:
?? DO NOT COPY THE WHOLE THING, CHANGE *Timothy Henderson* TO YOUR NAME ?? 
```sh
docker run -it --rm -p 3000:3000 `
    -v $PWD\:/course `
    -v $HOME\pl_ag_jobs:/jobs `
    -e HOST_JOBS_DIR=/c/Users/Timothy Henderson/pl_ag_jobs `
    -v /var/run/docker.sock:/var/run/docker.sock `
    prairielearn/prairielearn
```
**NOTE:** Change *Tim* into the name of your account, or try to navigate to `c/Users/` and look for your Window's name. Then substitute such name into the command (e.g.: `c/Users/{{Your User Name}}/pl_ag_jobs`)

#### If you somehow use Docker with a Window WSL2 Linux container, then do these inside the work folder:
```sh
docker run -it --rm -p 3000:3000 \
    -v "$PWD":/course \
    -v $HOME/pl_ag_jobs:/jobs \
    -e HOST_JOBS_DIR=$HOME/pl_ag_jobs \
    -v /var/run/docker.sock:/var/run/docker.sock \
    --add-host=host.docker.internal:172.17.0.1 \
    prairielearn/prairielearn
```

##### If you got any errors, I'm sorry but you will have to go look up a solution on the PrairieLearn's [External Grading documentation](https://prairielearn.readthedocs.io/en/latest/externalGrading/) ?? (or ask on SlackChannel!!!)

## Configuration
Now it's time to tweak some settings ?? 
### info.json
Most of the time, your `info.json` would look something like this:
```javascript
{
    "uuid": "...",
    "title": "...",
    "topic": "...",
    "tags": [...],
    "type": "v3"
}
```
To enable and use External Grading, add an option called `externalGradingOptions` and change the option `enable` inside to `true`. Also, add an option called `gradingMethod` and set it into `External`: 
```javascript
{
    ...
    "gradingMethod": "External",
    "externalGradingOptions": {
        "enabled": true
        ...
    }
}
```
Then there are a few more options you can change to make it betters, but I won't go in-depth into them. If you want to explore more you can visit PrairieLearn's External Grading page.
### Picking a supported language
Now you have 2 options, if you plan to grade **Python, C, or Java**, PrairieLearn provides us with their Auto Grader for those 3 languages and we will be using those.
In the unfortunate scenarios that you don't want to, can't specify the student's programming language, or the language is different from those 3. Then you will have to make your own Autograder for your own language I think ?? . (At that point you should just switch to Manual Grading)

The following are **recommended** configurations for using PrairieLearn's Autograder for the 3 supported programming languages mentioned above. You have to determine which programming language you want the student to use for your question and use the corresponding configuration:
#### Python:
```javascript
{
    ...
    "gradingMethod": "External",
    "externalGradingOptions": {
        "enabled": true,
        "image": "prairielearn/grader-python",
        "entrypoint": "/python_autograder/run.sh"
    }
}
```

#### C:
```javascript
{
    ...
    "gradingMethod": "External",
    "externalGradingOptions": {
        "enabled": true,
        "image": "prairielearn/grader-c",
        "entrypoint": "python3 /grade/tests/test.py"
    }
}
```
**NOTE**: for the C's `info.json`. The `entrypoint` option should be set to the *test.py* file in your &lt;question&gt;/tests/ folder. If you don't know what that means, don't worry, just substitute in your question-id name into `"entrypoint": "python3 /{{yourQuestionFolderName}}/tests/test.py"` 

#### Java:
```javascript
{
    ...
    "gradingMethod": "External",
    "externalGradingOptions": {
        "enabled": true,
        "image": "prairielearn/grader-java",
        "entrypoint": "autograder.sh"
    }
}
```

## Next Steps
Now that the setup steps are done. We can finally move on actually worked on creating our Tests for the student's submitted code. Each of the Autograder handles their tests mostly similarly, but I think there's some small difference here and there ?? We are now 30% done with creating our External Grading question.

The next steps would be heading to my Python's Autograding wiki page (If I've created it ?? ) or the other 2 languages (which I probably will never get to ??, if someone else be willing to write up their wiki I will credit you ?? ):

- [x] Python Autograder wiki
- [ ] C Autograder wiki
- [ ] Java Autograder wiki

P.S.: If you don't see my wiki and want to get started right away. [Here](https://prairielearn.readthedocs.io/en/latest/python-grader/) is the link to the PrairieLearn's documents on Python, there's also C and Java docs also.
##### Also I would recommend creating a tag for the External question type. That would help to discern the normal Internal Grading questions versus External ?? 