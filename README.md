# routing-ci

Public configuration for the CF Routing team's CI pipelines

[CI Dashboard: `dashboard.routing.cf-app.com`](http://dashboard.routing.cf-app.com)

# routing-ci

# routing-ci

To login as admin

- use `source ~/workspace/routing-ci/scripts/script_helpers.sh`
- cf_login <ENV>

### Dashboard config

#### DNS
 - `cf-app.com` base domain includes an NS record that delegates `routing.cf-app.com` to [DNS Zone `routing-team`](https://console.cloud.google.com/net-services/dns/zones/routing-team?project=cf-routing)
 - CNAME record `axxxxxxxl.routing.cf-app.com` is required for domain verification.  If you remove it, everything breaks!
 - Then `dashboard.routing.cf-app.com` is a CNAME for `c.storage.googleapis.com` in order to support [GCP Static Website Hosting](https://cloud.google.com/storage/docs/hosting-static-website)

#### Storage
 - There's a GCP [storage bucket for `dashboard.routing.cf-app.com`](https://console.cloud.google.com/storage/browser/dashboard.routing.cf-app.com?project=cf-routing)
 - It is configured to serve the contents of the `index.html` file in the storage bucket
 - That file defines a simple `iframe` that uses the `htmlpreview.github.io`
   ```
   curl dashboard.routing.cf-app.com
   ...
       <iframe src="http://htmlpreview.github.io/?https://github.com/cloudfoundry/routing-ci/blob/master/public-dashboard.html"></iframe>
   ```
  - the html preview is of the [`public-dashboard.html` file in this repo](public-dashboard.html)
  - that file contains its own `iframe`s to show various views from our Concourse server

### Why use two layers of indirection?
We could put the Concourse iframes directly in the `index.html` file.  But that file is difficult to update: you have to use the GCP console, or GCP API.  This way, we can make changes to the dashboard layout with a simple `git push` to this repo.
