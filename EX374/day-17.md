#### Advanced Template usage.

To make templates more powerful, we can apply variables and conditionals. 
- variables are placed between `{{ }}` delimiters
  - `{{ ansible_facts['distribution'] }}`
- conditionals are placed between `{% EXPR %}` delimiters
  ```
  {% for file in myfiles %}
    {{ file }}
  {% endfor %} 
   ```
