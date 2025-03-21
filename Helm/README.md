# Helm Charts for Multi-Environment Kubernetes Deployment

This task is about creating reusable Helm charts that can be deployed in to one of multiple Azure Kubernetes Service (AKS) environments. Your solution must not have any hardcoded values but rather use values or values files that are provided to the Helm command.

Ideally it would be great to run through the submission on a kubernetes cluster. You can use MiniKube, Kind, or anything that could help to demonstrate your solution. It should not take more than an hour. If it is taking longer, it is acceptable to present what you could accomplish in the time-boxed period.

Please run through each of the parts in sequence. At each stage make a copy of your chart so that we may see how you evolve your solution.

## Part 1

Produce a Helm chart that will allow a user to deploy one of the three following sites, web, app, api, individually. Each site should be based on deploying a replicaset of 3 pods. You can use the Hello World image as an example.

All sites will be behind their own ingress controller which is deployed separately and does not form part of this chart. However, do demonstrate how each rule will be applied to its corresponding controller.

Each site will be a subdomain of example.com `<site>.example.com` i.e.
- web.example.com
- api.example.com
- app.example.com

for which ingress rules will be required. Further to this, we will use an external monitoring solution that will test `/healthcheck/[GUID]` against each site to determine if it is up or not. The GUID is not known so your ingress rule should compensate for that.

## Part 2

The platform has gone global and we are now expanding into three regions. The above domains will now need to be prefixed with a region identifier - 'region[1..3]-', `region<identifier>-<site>.example.com` i.e.
- region1-web.example.com
- region3-api.example.com

however, region1 only, must maintain the original list of domains above. Change the above helm chart so that all three regions could be rendered with the minimum number of values files.

## Part 3

The company has now expanded with development teams worldwide. We now need to provide development and staging versions of all our sites. It has been agreed that URL's will follow this format - `region<identifier>-<site>-<environment>.example.com`

For example:
- region1-web-dev.example.com
- region2-app-stg.example.com
- region3-api-dev.example.com

Amend your helm chart to compensate for this addition. Remember to maintain the ability to deploy to production. This can be classed as another environment i.e. prd, but remember the urls will not contain this info like development or staging . Deployers of the chart must be able to specify a combination of the following parameters to render any one site:
- region [1-3]
- site [app, api, web]
- environment [stg, dev, prd]

## Bonus

Can we deploy ingress controller at the same time we deploy each site? You do not have to do this part, however, subject to time, we can talk about your ideas.

## Show & Tell

##### Walk us through the code detailing all the decisions you made along the way for