**Pod prioritization in kubernetes**

Prioritise your critical pods to get scheduled over other pods in Kubernetes.

For this, all you need is to know about "PriorityClasses" in Kubernetes and how it can help you in doing so.

✅ What is Pod Priority?

      👉 Pods can have priority in Kubernetes which indicates the importance of pod relative to other pods.

      👉 If a Pod cannot be scheduled, the scheduler tries to preempt (evict) lower priority Pods to make scheduling of the pending Pod possible.

      👉 This concept is pretty much similar how Systemd kills lower priority processes consuming high memory in order to keep running the higher priority processes when the system     goes low on memory.

      👉 Proper scheduling of the pods is important for your critical applications to be run on k8s without any issues.

      👉 The priority of a pod can be set using "PriorityClasses" in Kubernetes.

✅ What is PriorityClass and how it is used to prioritize a pod?

      👉 A PriorityClass is a non-namespaced object that defines a mapping from a priority class name to the integer value of the priority.

      👉 The name is specified in the name field of the PriorityClass object's metadata.

      👉 The value is specified in the required value field. The higher the value, the higher the priority.

      👉 Once the PriorityClass is created, you can use it in the Pod's spec field "priorityClassName" and map it to the PriorityClass name created.

      👉 The priority admission controller uses the priorityClassName field and populates the integer value of the priority.

✅ How does a Priority Pod gets scheduled?

      👉 When Pod priority is set, the scheduler orders pending Pods by their priority.

      👉 A pending Pod with higher priority is placed ahead of other pending Pods with lower priority in the scheduling queue.

      👉 As a result, the higher priority Pod may be scheduled sooner than Pods with lower priority if its scheduling requirements are met.

  👉 If such Pod cannot be scheduled, scheduler will continue and tries to schedule other lower priority Pods.

  👉 Higher priority pods will never block scheduling of other pods. It's just they will get a higher preference is scheduling if all their requirements are met at that point.

🔖 Pod priority can be very useful for running our critical applications. Check out the below links to learn more about Pod priority:
