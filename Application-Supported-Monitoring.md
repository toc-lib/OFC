# Application Supported Monitoring

## Goals

- Must be as similar as possible across applications
- Must make it easier for Site Operations, Load Balancers and Monitors to monitor the application
- Must have a minimum number of mandatory requirements
- Must be simple to implement
- Must be contained in a simple directory structure so it is easy to block from external access

## Why?

Monitoring the many applications is quite a complex task. The behaviour of most applications is quite complex and it is often hard to find the sweet spot to monitor. It is nearly impossible to make a check hit a single URL to give an overall health of the application. These requirements have been designed to allow Site Operations to build a single monitoring check that can be used across the entire suite of applications. This way we can easily configure our LB to drop a misbehaving application server from seeing production traffic, or automatically page a sysadmin when the application enters a degraded mode.

## How?

We propose a set of URLs with defined behaviours across all applications. By defining a singular set of URLs that can reuse the same monitoring scripts across the whole infrastructure and development can reuse the implementation code across multiple applications, every endpoint should include the instance name or a similar unique id to help addressing the problem instance.

### Required Endpoints

| **URL**               | **Hitrate**                               | **Purpose**                                                  |
| --------------------- | ----------------------------------------- | ------------------------------------------------------------ |
| /diagnostic           | ad hoc                                    | Lists the health check endpoints that this application supports. |
| /diagnostic/liveness  | often - tied to the probe's configuration | Contains no content and only returns either a HTTP 200 if the application is successfully deployed, or HTTP 500 if not.<br/>This indicates that the service is installed and up and running. <br/>**This should not invoke any external dependencies.<br/>If terminating the instance and creating a new one won't fix the problem, then this endpoint should continue to return 200 OK.<br/>In AWS, the reaction to failures from this endpoint is for the ELB to terminate the instance and create a new one.  Same in k8s, it will restart the container.** |
| /diagnostic/diagnosis | ad hoc                                    | Returns a human readable message to aid diagnosis of the error condition, check all the relevant resources like if there is a cluster, every node in the cluster will be checked.<br/>**This one will go through every denpendency even if it detects an error.** |
| /diagnostic/readiness | often - tied to the probe's configuration | Contains a simple OK with HTTP 200 if all the checks are good, or the first error result with HTTP 500 if not.<br/>This indicates that the service is not workable because an external dependency has failed.<br/>**This checks the dependent resources only for this call, like there is a cluster, only checks the node which this request is routed to.<br/>This one should immediately return the error once it gets a failed check.<br/>K8s will remove the pod from the service pool, AWS ELB wouldn't help the situation if set this as the health check.** |
| /diagnostic/version   | ad hoc                                    | Returns the version number of the application.               |

### Optional Endpoints

| **URL**           | **Hitrate** | **Purpose**                                                 |
| ------------------ | ------------ | ------------------------------------------------------------ |
| /diagnostic/config | ad hoc       | Returns the configuration settings of the application, for example the database server, the paths and such. |
