## Task 1: Create a ConfigMap - Imperative

**Objective:** Create a ConfigMap named `app-config` containing four environment-specific keys without using a YAML file.

* **Command:** `kubectl create configmap app-config --from-literal=APP_ENV=production --from-literal=APP_PORT=8080 --from-literal=APP_COLOR=blue --from-literal=MAX_CONNECTIONS=100`.


* **Verification:** Confirmed the keys via `kubectl describe configmap app-config`.


* **Key Concept:** Unlike hardcoding variables in a Pod, ConfigMaps allow you to decouple environment-specific configuration from the application container, making the image more portable across different environments (e.g., dev, prod).
![Task X Results](./part1.png)
---

## Task 2: Inject ConfigMap as Environment Variables

**Objective:** Create a Pod named `env-pod` that automatically injects all keys from `app-config` as environment variables.
* **Method:** Utilized `envFrom` with `configMapRef: app-config` in the Pod specification.
* **Verification:** Ran `kubectl logs env-pod` and `env | grep APP` to verify all four keys were present in the container runtime.
* **Key Concept:** `envFrom` is more efficient than manual entry because it automatically updates the Pod's environment if new keys are added to the ConfigMap, reducing YAML verbosity.
![Task X Results](./part2.png)
---

## Task 3: Inject Specific Keys with Custom Names

**Objective:** Create a Pod named `selective-pod` that maps specific ConfigMap keys to custom environment variable names inside the container.

* **Mapping:** * `APP_COLOR` → `COLOR` 
* `APP_PORT` → `PORT` 

* **Verification:** Used `kubectl exec` to confirm `MAX_CONNECTIONS` was **not** visible, as only specified keys were injected.


* **Key Concept:** Selective injection is preferred when a containerized application expects a specific variable name (like `PORT`) that doesn't match your organizational ConfigMap naming convention (like `APP_PORT`).


![Task X Results](./part3.png)
---

## Task 4: Create a Secret and Inspect It

**Objective:** Create an Opaque Secret named `db-secret` for database credentials using imperative methods to handle base64 encoding.

* **Command:** `kubectl create secret generic db-secret --from-literal=DB_USER=admin --from-literal=DB_PASSWORD=p@ssw0rd123 --from-literal=DB_NAME=myapp`.
* **Verification:** Decoded the `DB_USER` value using `kubectl get secret db-secret -o jsonpath='{.data.DB_USER}' | base64 -d`.
* **Key Concept:** Base64 is a form of **encoding**, not encryption. It is used to ensure binary data is safe for transport in YAML/JSON, but it offers no security without additional layers like Encryption at Rest or Secret Stores.

![Task X Results](./part4.png)

---

## Task 5: Inject Secret as Environment Variables

**Objective:** Create `db-pod` to inject `db-secret` values and verify they are decoded into plain text within the container.

* **Verification:** Confirmed through `kubectl logs db-pod` that the application sees the decoded values (e.g., `admin`).
* **Security Check:** Ran `kubectl describe pod db-pod`. While the Secret name is visible, the actual values remain hidden in the Pod description.


![Task X Results](./part5.png)
---

## Task 6: Inject ConfigMap + Secret Together

**Objective:** Create `fullapp-pod` to demonstrate the combined pattern of using both non-sensitive and sensitive configuration sources.

* **Implementation:** Used two `envFrom` blocks—one for `configMapRef` and one for `secretRef`.


* **Best Practice:**
* **ConfigMap:** Use for non-sensitive data like ports, feature flags, or color schemes.


* **Secret:** Use for sensitive data like passwords, API keys, and certificates.

![Task X Results](./part6.png)



---

## Task 7: Deployment with ConfigMap + Secret

**Objective:** Apply a Deployment named `webapp-configured` with 3 replicas to ensure consistent configuration across multiple Pods.

* **Verification:** Confirmed all 3 replicas inherited the same `APP_ENV` and `DB_USER` variables.


* **Advantage:** Using ConfigMaps/Secrets with Deployments allows you to update configuration for an entire fleet of Pods simultaneously without modifying the container image or hardcoding values in every replica spec.
![Task X Results](./part7.png)
