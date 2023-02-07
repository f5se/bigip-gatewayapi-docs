# RBAC
RBAC (role-based access control) is the standard used for Kubernetes authorization. This allows users to configure who can perform actions on resources in specific scopes. RBAC can be used to enable each of the roles defined above. In most cases, it will be desirable to have all resources be readable by most roles, so instead we'll focus on write access for this model.

## Write Permissions for Simple 3 Tier Model

| | GatewayClass | Gateway | Route |
| --- | --- | --- | -- |
| Infrastructure Provider | Yes | Yes | Yes |
| Cluster Operators | No | Yes | Yes | 
| Application Developers | No | No | Yes |
