 - Kubernetes, _namespaces_ provide a mechanism for ==isolating groups of resources== within a ==single cluster==.
 - Names of resources need to be unique within a namespace, but not across namespaces.

## Why use Kubernetes namespaces?

There are many use cases for Kubernetes namespaces, including:

- Allowing teams or projects to exist in their own virtual clusters ==without fear of impacting each other’s work.==
    
- Enhancing role-based access controls (RBAC) by ==limiting users and processes to certain namespaces.==
    
- Enabling the ==dividing of a cluster’s resources== between multiple teams and users via resource quotas.
    
- Providing an easy ==method of separating development, testing, and deployment of containerized applications== enabling the entire lifecycle to take place on the same cluster.
