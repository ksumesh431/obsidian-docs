Directory structure should be charts > chart.yaml, values.yaml

``` 
 helm package helm -d charts/
 helm repo index charts
 helm package helm -d charts/
 helm repo index charts
```

- package the helm chart
`helm package helm -d charts/`

- generate index.yml
`helm repo index charts`

push to github and generate github pages.
now can add the repo using github pages url
example : helm add *reponame* `url`