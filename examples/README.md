# Example

Below is a basic example. Post install/upgrade alerts will be sent to the https://bitsofinfo.slack.com `#bitsofinfo-dev` channel ([self signup to channel](https://join.slack.com/t/bitsofinfo/shared_invite/enQtNzE1OTM1MDY5MDYwLTk4MTc3MjA4Y2YwNjFkYjRlYjZjZWMyNWExY2QxN2JmMmMyOGViMzYzYmE5NjcyOGE5ZWFjYTM5MmVjNzUxMjc))

This should be enough to get your feet wet.

## Setup

Add to your local `/etc/hosts` (unless you have DNS setup) the contents of [hosts.txt](hosts.txt)

**IMPORTANT!:**
Before we continue we need to setup an `IngressController` [lets use Traefik, click here for setup instructions](https://github.com/bitsofinfo/appdeploy/blob/master/examples/TRAEFIK_SETUP.md)

Next, ensure the following Helm repos exists on your machine:
```
helm repo add bitsofinfo-appdeploy https://raw.githubusercontent.com/bitsofinfo/appdeploy/master/repo
helm repo add bitsofinfo-appconduits https://raw.githubusercontent.com/bitsofinfo/appconduits/master/repo
helm repo update
```

## Deploy some apps

In order to use `appconduits` we need some apps to send traffic to.

Let's deploy the following apps using the [appdeploy](https://github.com/bitsofinfo/appdeploy/) chart. These apps will serve as our targets for `conduits` that we will define in the example further below.

dogapp:1.0.0
```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name dogapp-1.0.0 \
  bitsofinfo-appdeploy/appdeploy --version 1.1.8 \
  --set app.name="dogapp" \
  --set image.repository="bitsofinfo/dogapp" \
  --set image.tag="1.0.0" \
  -f testapps.yaml
```

dogapp:2.0.0
```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name dogapp-2.0.0 \
  bitsofinfo-appdeploy/appdeploy --version 1.1.8 \
  --set app.name="dogapp" \
  --set image.repository="bitsofinfo/dogapp" \
  --set image.tag="2.0.0" \
  -f testapps.yaml
```

catapp:1.0.0
```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name catapp-1.0.0 \
  bitsofinfo-appdeploy/appdeploy --version 1.1.8 \
  --set app.name="catapp" \
  --set image.repository="bitsofinfo/catapp" \
  --set image.tag="1.0.0" \
  -f testapps.yaml
```

catapp:2.0.0
```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name catapp-2.0.0 \
  bitsofinfo-appdeploy/appdeploy --version 1.1.8 \
  --set app.name="catapp" \
  --set image.repository="bitsofinfo/catapp" \
  --set image.tag="2.0.0" \
  -f testapps.yaml
```

hogapp:1.0.0 (note! we also apply `hogapp.yaml`)
```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name hogapp-1.0.0 \
  bitsofinfo-appdeploy/appdeploy --version 1.1.8 \
  --set app.name="hogapp" \
  --set image.repository="bitsofinfo/hogapp" \
  --set image.tag="1.0.0" \
  -f testapps.yaml \
  -f hogapp.yaml
```

hogapp:2.0.0 (note! we also apply `hogapp.yaml`)
```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name hogapp-2.0.0 \
  bitsofinfo-appdeploy/appdeploy --version 1.1.8 \
  --set app.name="hogapp" \
  --set image.repository="bitsofinfo/hogapp" \
  --set image.tag="2.0.0" \
  -f testapps.yaml \
  -f hogapp.yaml
```

Wait for the installs to complete:
```
helm list

kubectl get all -n bitsofinfo-apps

kubectl get ingress -n bitsofinfo-apps

kubectl get jobs -n bitsofinfo-apps
```

Check traefik dashboard: https://bitsofinfo-traefik.test.local/dashboard/ to see the deployed backend

Verify the apps are up:
```
curl http://dogapp-stage-qa-1-0-0-80.local
curl http://dogapp-stage-qa-2-0-0-80.local
curl http://catapp-stage-qa-1-0-0-80.local
curl http://catapp-stage-qa-2-0-0-80.local
curl http://hogapp-stage-qa-1-0-0-80.local
curl http://hogapp-stage-qa-2-0-0-80.local
curl http://hogapp-stage-qa-1-0-0-443.local
curl http://hogapp-stage-qa-2-0-0-443.local
```

## Apply the conduits for the apps

Now lets actually wire up some custom conduits (`Services` & `Ingress`) for
the apps we just deployed. [Check out example.yaml](example.yaml) to see
the custom values we will be setting for the `appconduits` chart invocation.

```
helm install \
  --debug \
  --namespace bitsofinfo-apps \
  --name animals-test-conduits \
  bitsofinfo-appconduits/appconduits --version 1.0.11 \
  --set conduitname="animals" \
  -f example.yaml
```

You should see an alert at https://bitsofinfo.slack.com `#bitsofinfo-dev` channel ([self signup to channel](https://join.slack.com/t/bitsofinfo/shared_invite/enQtNzE1OTM1MDY5MDYwLTk4MTc3MjA4Y2YwNjFkYjRlYjZjZWMyNWExY2QxN2JmMmMyOGViMzYzYmE5NjcyOGE5ZWFjYTM5MmVjNzUxMjc))


## Test


Lets test the `live` conduits:

|Expect|target|
|---|---|
|dogapp:1.0.0|`curl http://animals.mydomain.com`|
|BLUE dogapp:1.0.0|`curl http://animals.mydomain.com/blue/`|
|GREEN dogapp:1.0.0|`curl http://animals.mydomain.com/green/`|
|RED catapp:1.0.0|`curl http://animals.mydomain.com/red/`|
|ORANGE catapp:1.0.0|`curl http://animals.mydomain.com/orange/`|
|BROWN hogapp:1.0.0|`curl http://animals.mydomain.com/brown/`|
|PINK hogapp:1.0.0|`curl http://animals.mydomain.com/pink/`|

Lets test the `canary` conduits:

|Expect|target|
|---|---|
|dogapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com`|
|BLUE dogapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com/blue/`|
|GREEN dogapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com/green/`|
|RED catapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com/red/`|
|ORANGE catapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com/orange/`|
|BROWN hogapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com/brown/`|
|PINK hogapp:1.0.0 or 2.0.0|`curl http://animals-canary.mydomain.com/pink/`|


## Cleanup:
```
helm delete --purge catapp-1.0.0 &
helm delete --purge catapp-2.0.0 &
helm delete --purge dogapp-1.0.0 &
helm delete --purge dogapp-2.0.0 &
helm delete --purge hogapp-1.0.0 &
helm delete --purge hogapp-2.0.0 &
helm delete --purge animals-test-conduits &
helm delete --purge bitsofinfo-traefik

```
