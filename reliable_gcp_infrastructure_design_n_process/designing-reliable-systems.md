# Designing Reliable Systems

- availability  (healthcheck, multiple zones and regions), durability (multiple copies of data, regulaer backup), scalability (multiple instances, autoscalers)
- implement fault-tolerant systems > single point of failure / correlated failures / cascading failures
- avoid overload failures > design patterns > circuit breaker / truncated exponential backoff
- resilient data storage > lazy deletion
- design scenarios
    - normal 
    - degraded
    - failure
- Disaster Recovery Plan
    - Analize scenarions
    - plan
    - implement 
    - test / simulation