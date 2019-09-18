# dns

Mriyam's DNS entries using GitHub Actions. Inspired from [this article](https://byparker.com/blog/2018/use-git-github-to-manage-your-dns-with-octodns/)

## Making changes

Modify the YAML file for the zone you'd like to update. The top-level key
is the subdomain value, and its value is a Hash/Dictionary of values.

For more details, check here - [Records Documentation](https://github.com/github/octodns/blob/master/docs/records.md)

Example records

1. A record with auto TTL
    ```yaml
    mail:
      ttl: 1
      type: A
      value: 1.1.1.1
    ```
2. CNAME record with Cloudflare Proxy disabled
    ```yaml
    www:
      octodns:
        cloudflare:
          proxied: false
      ttl: 3600
      type: CNAME
      value: mbtest.ga.
    ```
    
## GitHub Workflow

1. Create a new branch and check it out
2. Modify a record and commit the changes
3. Push the changes and submit a PR
4. Run octodns-sync without --doit and leave a comment with the output to show the changes you’re making
5. Get approval
6. Run octodns-sync with --doit to apply the changes
7. Verify the records are present in DNS and merge the PR