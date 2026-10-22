# Practical Assessment: Build a CI/CD Pipeline with Jenkins and Flux

---

## What you're being assessed on

One outcome, stated plainly:

> You change a line of code in your application repository, commit, and push. Without touching anything else, the change appears at `http://<VM_IP>:30024/`.

Everything below exists to make that possible. Tasks are ordered so each one builds on the last, and each has acceptance criteria you can check yourself before moving on.

Run `showcreds` on your VM at any point for your URLs and Jenkins login.

---

## About the starter code

**The repositories you forked contain deliberate defects.** Finding and fixing them is part of the assessment.

They are not subtle traps, and they are not evenly distributed — some files are fine. What they have in common is that each one surfaces at a different layer of the pipeline, and each announces itself if you look in the right place. None require you to guess: every one of them produces an error message, a failed status, or a log line that identifies the problem.

You won't be told how many there are or where they live. You will be told this: **if something fails, the starter code is a legitimate suspect.** Read the file you're working with rather than assuming it's correct.

Part A below is your check on the first kind. Everything after Part A is yours to fix.

---

## Rules

Follow all six. Breaking one counts against you even if the pipeline works.

1. **All three repositories stay private** — both GitHub forks and your Docker Hub repository.
2. **Images are built with Kaniko.** No `docker build`, no `docker:dind`, no mounting the host Docker socket.
3. **Deployment happens through Flux only.** Don't `kubectl apply`, `kubectl edit`, or `kubectl set image` your application resources. Use `kubectl` to inspect freely.
4. **No credentials in Git.** No tokens, no `.dockerconfigjson`, no kubeconfig — in either repository, at any point in its history.
5. **Don't change the NodePort.** `30024` is the only port that reaches your app from outside. See Orientation §5.
6. **Don't weaken Jenkins security.** No anonymous access, no disabled authentication.

---

## Part A — Confirm your environment

Before starting, verify the VM is ready. If any of these fail, raise it now rather than working around it.

```bash
showcreds                       # your URLs and Jenkins credentials
kubectl get nodes               # two nodes, both Ready
kubectl -n jenkins get pods     # jenkins-0 Running
flux --version                  # CLI present
git config --global --get user.email    # must return a value
```

Then open the Jenkins URL from `showcreds` in a browser and log in. **You should be prompted for credentials.** If Jenkins loads straight to a dashboard without asking, stop and report it.

Part A is the full extent of what you should report to an admin. If all five commands above succeed and Jenkins asks you to log in, your environment is sound, and anything that breaks from here is yours to diagnose.

---

## Task 1 — Give Jenkins its credentials

Jenkins needs to authenticate to two external services on your behalf: GitHub, to read and write your private repositories, and Docker Hub, to push images.

These are two different mechanisms, and knowing which goes where is part of the task. One lives in Jenkins' own credential store. The other has to be available to a build container as a mounted file, which means it lives in Kubernetes.

**Requirements**

- A Jenkins credential of type _Username with password_ holding your GitHub username and PAT, with the ID **`github-pat`**.
- A Kubernetes secret in the `jenkins` namespace named **`dockerhub`**, of type `kubernetes.io/dockerconfigjson`, holding your Docker Hub token.

Both names are referenced by the provided Jenkinsfile scaffold. They must match exactly.

**Acceptance criteria**

- Manage Jenkins → Credentials shows `github-pat`.
- `kubectl -n jenkins get secret dockerhub -o jsonpath='{.type}'` returns `kubernetes.io/dockerconfigjson`.
- Manage Jenkins → System → GitHub → **Test connection** reports your username and rate limit.

> The Test connection button is the fastest way to confirm your PAT works. Use it before you build anything.

---

## Task 2 — Create the pipeline job

Create a Jenkins Pipeline job that reads its definition from your `python-flaskapp` repository rather than from a script pasted into the job configuration.

**Requirements**

- A Pipeline job named `flaskapp`.
- Definition sourced from your fork, using the `github-pat` credential.
- The correct branch for your fork.

**Acceptance criteria**

- **Build Now** produces a build whose log shows the repository being cloned.
- The build reaches the scaffold's first stage and prints `Dockerfile found`.

The two TODO stages will fail. That's expected — they're your work in Tasks 3 and 4.

> A private repository returns `Repository not found` for a wrong name, a wrong token, an unscoped token, or a genuinely missing repository. Check the repository name first.

---

## Task 3 — Build and push the image

Complete the **Build and push image** stage in the Jenkinsfile.

**Requirements**

- Build the image from the `Dockerfile` in the repository using Kaniko.
- Push it to your private Docker Hub repository.
- Tag each build uniquely, so two builds never produce the same tag.
- Commit the completed Jenkinsfile to your fork.

**Acceptance criteria**

- The build log shows the base image being pulled and the image being pushed.
- Your Docker Hub repository shows the new tag.
- Your Docker Hub repository is still marked **Private**.
- A second build produces a second, different tag.

> The Kaniko binary is at `/kaniko/executor`. Its credentials are already mounted by the scaffold's pod definition, so no login step is needed. Work out which flags it needs for build context, Dockerfile location, and destination.
> 
> If the build fails, read the log before assuming your flags are wrong. Kaniko reports problems in the `Dockerfile` distinctly from problems with how it was invoked, and it names the offending line.
> 
> That last acceptance criterion is not busywork. Docker Hub creates missing repositories automatically on push, and auto-created repositories are **public**. If your destination path has a typo, the push succeeds into a new public repository while your private one stays empty.

---

## Task 4 — Hand off to GitOps

Complete the **Update deployment-config** stage. This is where CI ends and GitOps begins: Jenkins' final act is a commit, not a deployment.

**Requirements**

- Clone your `deployment-config` fork using the credential bound in the stage.
- Update the image reference in the application's Deployment manifest to the tag this build just pushed.
- Commit and push.

**Acceptance criteria**

- `deployment-config` has a new commit authored by the pipeline.
- The commit's diff shows the image tag changing on exactly one line.
- Running the build again produces another commit with the newer tag.

> An empty commit means your edit matched nothing. This fails quietly — the build goes green, the commit exists, and nothing changed. Check the diff on GitHub, not just that the commit appeared.
> 
> Git refuses to commit without an author identity, and the build container is not your laptop.

---

## Task 5 — Trigger builds automatically

A push to `python-flaskapp` must start a build with no manual action.

**Requirements**

- A webhook on your fork pointing at your Jenkins instance.
- The job configured to respond to it.

**Acceptance criteria**

- GitHub → repository Settings → Webhooks → **Recent Deliveries** shows a `push` event with a successful response.
- Pushing a commit starts a build within a minute, with nobody clicking anything.

> The Recent Deliveries panel is authoritative for one specific question: did GitHub reach Jenkins? It answers that and nothing else. A successful response there with no build starting means the payload arrived and Jenkins didn't act on it — a different problem in a different place.
> 
> GitHub sends a `ping` event when a webhook is created. It succeeds and looks encouraging. It is not a push event.

---

## Task 6 — Bootstrap Flux

Install Flux into the cluster such that Flux manages its own configuration from your `deployment-config` repository.

**Requirements**

- Use `flux bootstrap`, not `flux install`. The difference matters: bootstrap commits Flux's own manifests to your repository and configures Flux to reconcile itself from them.
- Target your `deployment-config` fork.
- Flux's configuration lives under a `clusters/` path of your choosing.

**Acceptance criteria**

- `flux check` passes.
- `kubectl -n flux-system get pods` shows all controllers Running.
- Your `deployment-config` repository has a new commit adding a `clusters/<something>/flux-system/` directory.
- `flux get sources git` shows the `flux-system` source `Ready: True`.

> Run `flux bootstrap github --help` before running the command. It needs your GitHub token, and it doesn't take it as a flag.
> 
> Bootstrap creates a deploy key on your repository. If a bootstrap attempt fails partway, delete the deploy key and the committed directory before retrying, or the next attempt will conflict.
> 
> Remember which `clusters/` path you chose. You need it in Task 7.

---

## Task 7 — Deploy the application

Flux is now running and watching your repository, but it isn't yet applying your application. Bootstrap only reconciles the path it created.

**Requirements**

- Tell Flux to apply the manifests in `apps/flask-app/`.
- The instruction itself must be committed to your repository, not applied by hand. If it only exists in the cluster, it doesn't survive a rebuild — and that defeats the point of GitOps.
- The application must pull its image successfully.

**Acceptance criteria**

- `flux get kustomizations` shows **two** Kustomizations, both `Ready: True`.
- `kubectl -n flask-app get pods` shows a pod `Running` with `0` restarts — not `ImagePullBackOff`, not `CrashLoopBackOff`.
- `kubectl -n flask-app logs deploy/...` shows the application started, with no errors.
- `kubectl -n flask-app get endpoints` shows an endpoint for the Service, not `<none>`.
- `curl http://<VM_IP>:30024/` from **your own machine**, not the VM, returns the app.

> Your image is private, so the cluster must authenticate to pull it. The Deployment manifest already declares which secret it expects. Secrets exist in one namespace only — Orientation §4.
> 
> Ordering catches people here. That namespace doesn't exist until Flux creates it.
> 
> These five criteria fail in different places on purpose. A Kustomization that won't apply, an image that won't pull, a container that starts and then dies, a Service with no endpoints, and a port nothing reaches are five separate problems with five separate diagnostics. Work out which one you have before changing anything. `kubectl describe` for events, `kubectl logs` for what the application itself said.
> 
> Testing with `curl localhost` on the VM proves nothing about external access. It skips the firewall and the host port mapping, which are two of the five layers in Orientation §5.

---

## Task 8 — Demonstrate the loop

**Requirements**

Change something visible in the Flask app — the string it returns is fine. Commit and push. Then touch nothing.

**Acceptance criteria**

Within a few minutes, and with no manual intervention at any point:

1. A Jenkins build starts on its own.
2. A new tag appears in Docker Hub.
3. A new commit appears in `deployment-config`.
4. Flux reports a new revision.
5. A new pod rolls out in `flask-app`.
6. `http://<VM_IP>:30024/` returns your changed string.

This is the assessment. The previous seven tasks are how you get here.

---

## Part B — When you finish

There is nothing to submit. Your work is evaluated from the VM and from your repositories, so what matters is the state you leave behind.

**Leave everything running.** Don't delete the cluster, uninstall Flux, remove the Jenkins job, or tidy up secrets. A cleaned-up VM looks identical to one where nothing was ever built.

**Commit and push everything.** Anything that exists only on the VM's disk, or only in the running cluster, is invisible. Specifically:

- Your completed `Jenkinsfile`, pushed to `python-flaskapp`.
- Your Flux Kustomization, committed to `deployment-config`.
- Every fix you made to the starter code, pushed to whichever repository it belongs in.

A Kustomization applied with `kubectl` but never committed is the classic version of this. It works, the cluster looks right, and the repository says otherwise. Rule 3 exists for that reason.

**Before time is up, confirm:**

```bash
# Nothing uncommitted, nothing unpushed, in both repositories
git status
git log origin/main..HEAD          # must be empty

# The pipeline's final state
flux get sources git
flux get kustomizations
kubectl -n flask-app get pods,svc,endpoints
```

Then load `http://<VM_IP>:30024/` one last time from your own machine and confirm it returns your most recent change.

VM access is withdrawn after the assessment window closes, so there is no opportunity to push a forgotten commit afterwards.

---

## When you're stuck

Work the chain rather than changing several things at once. Each of these has a definite yes or no answer, and finding the last one that says yes tells you where to look:

|Question|Where the answer is|
|---|---|
|Did GitHub reach Jenkins?|Webhook → Recent Deliveries|
|Did Jenkins act on the payload?|Job page → GitHub Hook Log|
|Did the image get pushed?|Docker Hub, not the build log|
|Did Jenkins commit the tag?|The commit diff on GitHub|
|Did Flux see the commit?|`flux get sources git`|
|Did Flux apply it?|`flux get kustomizations`|
|Did the pod start?|`kubectl -n flask-app describe pod ...`, read the events|
|Can the outside world reach it?|`curl` from your own machine|

Two error messages you will probably meet, and what they actually mean:

- **`Repository not found`** — GitHub won't confirm a private repository exists to a caller it hasn't authorized. Wrong name, wrong token, unscoped token, and missing repository all produce this. Check the name first.
- **`insufficient_scope` / `pull access denied` from `docker.io`** — the _cluster_ can't authenticate to your registry. It says nothing about whether your token is valid.

