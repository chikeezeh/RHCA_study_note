#### Creating Job template surveys

User input can be collected while running ansible via the CLI by using `vars_prompt`. However, this isn't supported on ansible automation platform. The alternative is to use `Job Template surveys`. The survey will prompt for variables when the job is started. 

There are different answer types for the variables in the survey;
- `Text`: single line of text
- `TextArea`: text of multiple lines
- `Password`: treated as sensitive information
- `Multiple choice (single select)`: list of options, were one item can be selected. 
- `Multiple choice (multiple select)`: list of options, were multiple items can be selected.
- `Integer`: an integer number
- `Float`: a floating point number

