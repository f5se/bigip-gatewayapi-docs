# Roles and personas
In the original design of Kubernetes, Ingress and Service resources were based on a self-service model of usage; developers who create Services and Ingresses control all aspects of defining and exposing their applications to their users.

We have found that the self-service model does not fully capture some of the more complex deployment and team structures that our users are seeing. Gateway API is designed to target the following personas:

•	Infrastructure provider: The infrastructure provider (infra) is responsible for the overall environment that the cluster(s) are operating in. Examples include: the cloud provider (AWS, Azure, GCP, ...) or the PaaS provider in a company.

•	Cluster operator: The cluster operator (ops) is responsible for administration of entire clusters. They manage policies, network access, application permissions.

•	Application developer: The application developer (dev) is responsible for defining their application configuration (e.g. timeouts, request matching/filter) and Service composition (e.g. path routing to backends).

Although these roles can cover a wide variety of use cases, some organizations may be structured slightly differently. Many organizations may also have a fourth role that sits between "cluster operator" and "application developer":

•	Application admin: The application admin has administrative access to some namespaces within a cluster, but not the cluster as a whole.

We expect that each persona will map approximately to a Role in the Kubernetes Role-Based Authentication (RBAC) system and will define resource model responsibility and separation.
Depending on the environment, multiple roles can map to the same user. For example, giving the user all the above roles replicates the self-service model.


