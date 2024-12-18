# Lab 4 : Création d'un serveur CentOS avec une IP privée statique et Nginx

## 🌟 Objectif

L'objectif de ce laboratoire est de :

1. Créer une machine virtuelle CentOS avec Vagrant.
2. Assigner une **adresse IP privée statique** (évitant les plages par défaut de VirtualBox).
3. Installer et configurer manuellement un serveur Nginx.
4. Se connecter à la VM via **SSH** depuis la machine locale.

## 📊 Prérequis

Assure-toi d'avoir les outils suivants installés :

- **Vagrant**
- **VirtualBox**
- Une connexion Internet fonctionnelle

## 🚀 Étapes du Lab

### 1. Initialisation de la VM avec CentOS 7

Créez une machine virtuelle basée sur CentOS 7 :

```bash
vagrant init centos/7
```

---

### 2. Configuration du Vagrantfile avec une IP statique

Voici le `Vagrantfile` utilisé pour ce lab :

```ruby
# Variables pour la configuration
RAM_MACHINE = "2048"
CPU_MACHINE = 2
OS_BOX = "centos/7"
VERSION_BOX = "2004.01"
PRIVATE_IP = "10.0.0.10"

# Configuration de la machine virtuelle
Vagrant.configure("2") do |config|
  config.vm.box = OS_BOX
  config.vm.box_version = VERSION_BOX

  # Configuration des ressources
  config.vm.provider "virtualbox" do |vb|
    vb.memory = RAM_MACHINE
    vb.cpus = CPU_MACHINE
  end

  # Configuration réseau avec une IP statique
  config.vm.network "private_network", ip: PRIVATE_IP
end
```

---

### 3. Gestion des plages IP dans VirtualBox

VirtualBox limite par défaut les plages IP autorisées pour les réseaux privés. Pour autoriser **10.0.0.0/24** :

1. Crée ou modifie le fichier `/etc/vbox/networks.conf` :

```bash
sudo nano /etc/vbox/networks.conf
```

2. Ajoute les lignes suivantes :

```plaintext
* 192.168.10.0/24
* 10.0.0.0/24
```

3. Redémarre le service VirtualBox :

```bash
sudo systemctl restart vboxdrv
```

---

### 4. Déploiement et connexion à la VM

Démarrez la VM :

```bash
vagrant up
```

Connectez-vous à la VM avec SSH :

```bash
ssh -i /path/to/private/key vagrant@10.0.0.10
```

---

### 5. Installation manuelle de Nginx

#### Modifier les dépôts pour CentOS 7

Pour résoudre le problème des dépôts obsolètes, remplacez le contenu du fichier `/etc/yum.repos.d/CentOS-Base.repo` :

```ini
[base]
name=CentOS-$releasever - Base
baseurl=http://vault.centos.org/7.9.2009/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[updates]
name=CentOS-$releasever - Updates
baseurl=http://vault.centos.org/7.9.2009/updates/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras
baseurl=http://vault.centos.org/7.9.2009/extras/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://vault.centos.org/7.9.2009/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

#### Installer et configurer Nginx

1. Nettoyez les caches et mettez à jour les paquets :

   ```bash
   sudo yum clean all && sudo yum update -y
   ```

2. Installez Nginx :

   ```bash
   sudo yum install -y epel-release
   sudo yum install -y nginx
   ```

3. Démarrez et activez Nginx :

   ```bash
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

---

### 6. Tester le serveur Nginx

#### Vérifiez l'adresse IP de la VM

Depuis la VM, exécutez :

```bash
ip a
```

#### Accéder au serveur

Ouvrez votre navigateur et accédez à :

```
http://10.0.0.10
```

Vous devriez voir la page par défaut de Nginx.

---

## 🔧 Difficultés Rencontrées

### Problème : IP hors des plages autorisées

VirtualBox a signalé que l'adresse IP **10.0.0.10** était hors des plages autorisées.

#### Solution :
Ajout des plages personnalisées dans le fichier `/etc/vbox/networks.conf` :

```plaintext
* 192.168.10.0/24
* 10.0.0.0/24
```

### Problème : Dépôts CentOS obsolètes

Les URL par défaut de CentOS 7 ne fonctionnent plus.

#### Solution :
Remplacement des `mirrorlist` par des `baseurl` pointant vers `vault.centos.org`.

---

## 📊 Commandes Utiles

| Commande                          | Description                              |
|-----------------------------------|------------------------------------------|
| `vagrant init centos/7`           | Initialise le projet avec CentOS 7.     |
| `vagrant up`                      | Démarre la machine virtuelle.           |
| `vagrant ssh`                     | Accède à la VM via SSH.                |
| `sudo yum install -y nginx`       | Installe le serveur Nginx.              |
| `sudo systemctl start nginx`      | Démarre le service Nginx.               |
| `ip a`                            | Affiche l'adresse IP de la VM.          |

---

## 📈 Conclusion

Ce lab a permis de :

1. Configurer une machine virtuelle CentOS avec une **IP privée statique**.
2. Installer et configurer manuellement un serveur Nginx.
3. Surmonter les problèmes liés aux restrictions de VirtualBox et aux dépôts CentOS obsolètes.

🚀 Félicitations ! Votre serveur Nginx fonctionne sur CentOS avec une IP statique. 🚀

---
