# lightkube

Modern lightweight kubernetes module for python

**NOTICE:** This project is still in an early alpha stage and not suitable for production usage.

## Usage

Read a pod

```python
from lightkube import Client
from lightkube.resources.core_v1 import Pod

client = Client()
pod = client.ns.get(Pod, name="my-pod", namespace="default")
print(pod.namespace.uid)
```

List nodes
```python
from lightkube import Client
from lightkube.resources.core_v1 import Node

client = Client()
for node in client.list(Node):
    print(node.metadata.name)
```

Watch deployments
```python
from lightkube import Client
from lightkube.resources.apps_v1 import Deployment

client = Client()
for op, dep in client.ns.watch(Deployment, namespace="default"):
    print(f"{dep.namespace.name} {dep.spec.replicas}")
```

Create a config map
```python
from lightkube.resources.core_v1 import ConfigMap
from lightkube.models.meta_v1 import ObjectMeta

config = ConfigMap(
    metadata=ObjectMeta(name='my-config', namespace='default'),
    data={'key1': 'value1', 'key2': 'value2'}
)

client.ns.post(config)
```

Replace the previous config with a different content
```python
config.data['key1'] = 'new value'
client.ns.put(config)
```

Patch an existing config
```python
patch = {'metadata': {'labels': {'app': 'xyz'}}}
client.ns.patch(ConfigMap, name='my-config', namespace='default', obj=patch)
```

Delete a namespaced resource
```python
client.ns.delete(ConfigMap, name='my-config', namespace='default')
```

Scale a deployment
```python
from lightkube.resources.apps_v1 import Deployment
from lightkube.models.meta_v1 import ObjectMeta
from lightkube.models.autoscaling_v1 import ScaleSpec

obj = Deployment.Scale(
    metadata=ObjectMeta(name='metrics-server', namespace='kube-system'),
    spec=ScaleSpec(replicas=1)
)
client.ns.put(obj, 'metrics-server', 'kube-system')
```
