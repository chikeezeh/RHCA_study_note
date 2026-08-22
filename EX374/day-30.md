#### Using `lookup` plugin

Lookup plugings can be used in two ways;
- `lookup` read file content into a variable.
- `query` read file content that is stored as a list into a variable.

##### Types of `lookup` plugins

- `file`: reads content of a file
- `template`: processes the contents of a template
- `env`: uses an environment variable setup on a control node, for example password.
- `url`: gets the content from a URL
- `pipe` returns the output of a command executed on the control host. 