# dns

![Test on PR](https://github.com/mbtamuli/dns/workflows/Test%20on%20pull%20request/badge.svg)

![Deploy on push](https://github.com/mbtamuli/dns/workflows/Deploy%20on%20push/badge.svg)

Mriyam's DNS entries using GitHub Actions. Inspired from [this article](https://byparker.com/blog/2018/use-git-github-to-manage-your-dns-with-octodns/)

## Making changes

Modify the YAML file for the zone you'd like to update. The top-level key
is the subdomain value, and its value is a Hash/Dictionary of values.

For more details, check here - [Records Documentation](https://github.com/github/octodns/blob/master/docs/records.md)

**Note:** 
1. _Auto TTL on Cloudflare [equals 300 seconds](https://support.cloudflare.com/hc/en-us/articles/360017421192-Cloudflare-DNS-FAQ#whatdoestheautomaticttlvaluemean)_
2. _For proxied records, Cloudflare manages TTL, so the value doesn't apply_

## Running locally

From the root of the repo, run
```
docker run --rm -it \
    --env CLOUDFLARE_EMAIL="$CLOUDFLARE_EMAIL" \
    --env CLOUDFLARE_TOKEN="$CLOUDFLARE_TOKEN" \
    --volume $PWD/config:/config \
    parkr/octodns:v0.9.5 \
    octodns-sync --config-file ./config/production.yaml
```

If you want to actually make the change, just add `--doit` at the end.

```
docker run --rm -it \
    --env CLOUDFLARE_EMAIL="$CLOUDFLARE_EMAIL" \
    --env CLOUDFLARE_TOKEN="$CLOUDFLARE_TOKEN" \
    --volume $PWD/config:/config \
    parkr/octodns:v0.9.5 \
    octodns-sync --config-file ./config/production.yaml --doit
```

## Initializing another domain

Assuming the domain is on cloudflare, you need to run this from the root of the repo.
```
docker run --rm -it \
    --env CLOUDFLARE_EMAIL="$CLOUDFLARE_EMAIL" \
    --env CLOUDFLARE_TOKEN="$CLOUDFLARE_TOKEN" \
    -v $(pwd):/opt/dns \
    parkr/octodns:v0.9.5 \
    octodns-dump \
    --config-file /opt/dns/config/production.yaml \
    --output-dir /opt/dns/config \
    "example.com." \
    cloudflare
```

Example records

1. A record with minimum TTL.
    ```yaml
    mail:
      ttl: 120
      type: A
      value: 1.1.1.1
    ```
1. A record with TTL equivalent to `auto` TTL.
    ```yaml
    blog:
      ttl: 300
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

1. Create a new branch and check it out.
2. Modify a record and commit the changes.
3. Push the changes and submit a PR.
4. GitHub Actions runs `octodns-sync` to generate plan.
5. Verify the records in the output of the dry run and merge the PR.
6. GitHub Actions runs `octodns-sync --doit` to apply the plan.