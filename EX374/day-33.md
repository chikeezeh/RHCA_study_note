#### Fact Caching Caveats

- If a playbook uses facts but has `gather_facts: false`, it will fail if the cached facts expire. 
- A scheduled job to refresh cache can prevent cache from expiring.
- To use `gather_facts` in place of `cache`, run the playbook with the `--flush-cache` flag. 
