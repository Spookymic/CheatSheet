# Cheatsheet Kubernetes

## Concepts clés
| Objet | Rôle |
|---|---|
| **Pod** | plus petite unité, un ou plusieurs conteneurs |
| **Deployment** | gestion des réplicas et mises à jour (stateless) |
| **StatefulSet** | pods avec identité stable (bases de données) |
| **Service** | exposition stable d'un ensemble de pods (ClusterIP, NodePort, LoadBalancer) |
| **Ingress** | routage HTTP/HTTPS externe |
| **Namespace** | cloisonnement logique des ressources |
| **ConfigMap / Secret** | configuration et données sensibles |
| **PersistentVolume (PV) / PVC** | stockage persistant |
| **DaemonSet** | un pod par nœud (ex. agents, logs) |
| **Job / CronJob** | tâches ponctuelles ou planifiées |
| **Node** | machine (worker) qui exécute les pods |
| **kubelet / kube-apiserver** | agent nœud / API du cluster |

## Commandes kubectl
| Commande | Effet |
|---|---|
| `kubectl get pods` | lister les pods |
| `kubectl get pods -n monns` | lister dans un namespace |
| `kubectl get all` | lister toutes les ressources du namespace |
| `kubectl get nodes -o wide` | lister les nœuds (avec IP, version) |
| `kubectl get deploy,svc,rs` | deployments, services, replicasets |
| `kubectl describe pod nom` | détails et événements d'un pod |
| `kubectl logs nom-pod` | logs d'un pod |
| `kubectl logs -f nom-pod` | suivre les logs |
| `kubectl logs nom-pod -c ctr` | logs d'un conteneur précis |
| `kubectl exec -it nom-pod -- bash` | entrer dans le conteneur |
| `kubectl apply -f fichier.yaml` | appliquer une configuration (déclaratif) |
| `kubectl create -f fichier.yaml` | créer depuis un fichier |
| `kubectl delete pod nom` | supprimer un pod |
| `kubectl delete -f fichier.yaml` | supprimer les ressources du fichier |
| `kubectl scale deploy nom --replicas=3` | monter/baisser les réplicas |
| `kubectl rollout status deploy nom` | suivre un déploiement |
| `kubectl rollout restart deploy nom` | redémarrer un déploiement |
| `kubectl rollout undo deploy nom` | revenir à la version précédente |
| `kubectl port-forward pod 8080:80` | tunnel de port local |
| `kubectl run nginx --image=nginx` | créer un pod directement |
| `kubectl create deployment web --image=nginx` | créer un deployment directement |
| `kubectl expose deployment web --port=80 --type=NodePort` | exposer un deployment |
| `kubectl label pods nom app=v1` | ajouter un label |
| `kubectl top nodes` / `kubectl top pods` | consommation CPU / mémoire |
| `kubectl drain noeud` | évacuer un nœud avant maintenance |
| `kubectl cordon noeud` / `uncordon` | bloquer / autoriser les pods sur un nœud |
| `kubectl get events --sort-by=.lastTimestamp` | événements du cluster |

### Contextes / kubeconfig
| Commande | Effet |
|---|---|
| `kubectl config get-contexts` | lister les contextes |
| `kubectl config use-context prod` | basculer de contexte |
| `kubectl config current-context` | contexte actif |
| `kubectl config set-context ctx --namespace=monns` | définir le namespace par défaut |

## Manifest exemple
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
  labels: { app: web }
spec:
  replicas: 3
  selector:
    matchLabels: { app: web }
  template:
    metadata:
      labels: { app: web }
    spec:
      containers:
        - name: nginx
          image: nginx:1.27
          ports:
            - containerPort: 80
          env:
            - name: LOG_LEVEL
              value: "info"
          resources:
            requests: { cpu: "100m", memory: "128Mi" }
            limits:   { cpu: "500m", memory: "512Mi" }
          readinessProbe:
            httpGet: { path: /, port: 80 }
---
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector: { app: web }
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

## Outils associés
| Outil | Rôle |
|---|---|
| `minikube start` | cluster local mononœud |
| `kind create cluster` | cluster local via Docker |
| `kubeadm init` / `kubeadm join` | installation d'un cluster |
| `helm` | gestionnaire de paquets (charts) |
| `k9s` | interface TUI pour Kubernetes |
| `kubectx` / `kubens` | changer de contexte / namespace |
| `stern` | logs multi-pods |
| `kubectl debug node/noeud` | debugging (newer) |

## Dépannage
| Problème | Commandes utiles |
|---|---|
| Pod en `CrashLoopBackOff` | `kubectl logs pod`, `kubectl describe pod` |
| Pod en `Pending` | `kubectl describe pod` (ressources, noeud) |
| Image introuvable | `kubectl describe pod` → `ErrImagePull` |
| Accès réseau | `kubectl exec -it pod -- curl http://svc` |
| Nœud `NotReady` | `kubectl get nodes`, `systemctl status kubelet` |
