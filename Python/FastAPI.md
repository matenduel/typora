













# Deployment

# Standalone ?

### Example



### Gunicorn







## Container (Docker)

### Example

```docker
FROM python:3.9

WORKDIR /code

COPY ./requirements.txt /code/requirements.txt

RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

# 
COPY ./main.py /code/

# 
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80"]
```



### Replication - Number of Processes

If you have a cluster of machines with **Kubernetes**, Docker Swarm Mode, Nomad, or another similar complex system to manage distributed containers on multiple machines, then you will probably want to **handle replication** at the **cluster level** instead of using a **process manager** (like Gunicorn with workers) in each container.

One of those distributed container management systems like Kubernetes normally has some integrated way of handling **replication of containers** while still supporting **load balancing** for the incoming requests. All at the **cluster level**.

In those cases, you would probably want to build a **Docker image from scratch** as [explained above](https://fastapi.tiangolo.com/deployment/docker/#dockerfile), installing your dependencies, and running **a single Uvicorn process** instead of running something like Gunicorn with Uvicorn workers.



### One Load Balancer - Multiple Worker Containers[¶](https://fastapi.tiangolo.com/deployment/docker/#one-load-balancer-multiple-worker-containers)

When working with **Kubernetes** or similar distributed container management systems, using their internal networking mechanisms would allow the single **load balancer** that is listening on the main **port** to transmit communication (requests) to possibly **multiple containers** running your app.

Each of these containers running your app would normally have **just one process** (e.g. a Uvicorn process running your FastAPI application). They would all be **identical containers**, running the same thing, but each with its own process, memory, etc. That way you would take advantage of **parallelization** in **different cores** of the CPU, or even in **different machines**.

And the distributed container system with the **load balancer** would **distribute the requests** to each one of the containers with your app **in turns**. So, each request could be handled by one of the multiple **replicated containers** running your app.

And normally this **load balancer** would be able to handle requests that go to *other* apps in your cluster (e.g. to a different domain, or under a different URL path prefix), and would transmit that communication to the right containers for *that other* application running in your cluster.

### One Process per Container[¶](https://fastapi.tiangolo.com/deployment/docker/#one-process-per-container)

In this type of scenario, you probably would want to have **a single (Uvicorn) process per container**, as you would already be handling replication at the cluster level.

So, in this case, you **would not** want to have a process manager like Gunicorn with Uvicorn workers, or Uvicorn using its own Uvicorn workers. You would want to have just a **single Uvicorn process** per container (but probably multiple containers).

Having another process manager inside the container (as would be with Gunicorn or Uvicorn managing Uvicorn workers) would only add **unnecessary complexity** that you are most probably already taking care of with your cluster system.









# Referenece

1. https://fastapi.tiangolo.com/deployment/docker/#check-it