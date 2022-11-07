# AWS-CLI EC2 Apache Web Server
  [Quelle](https://towardsaws.com/how-to-use-aws-cli-to-launch-an-ec2-web-server-with-apache-9c20d07e07be)

### Cloud9 mit GitHub per SSH verbinden/verknüpfen

### Projekt erstellen

#### Projektverzeichnis erstellen
    
    mkdir AWS_CLI

#### Textdatei fuer Ressourcen erstellen

    touch resources.txt

#### AMI überprüfen

    aws ec2 describe-images --owners amazon --filters "Name=name, Values=amzn2-ami-hvm-2.0.????????.?-x86_64-gp2" "Name=state, Values=available" --query "reverse(sort_by(Images, &Name))[:1].ImageId" --output text
    
#### Keypair fürs Projekt erstellen

    aws ec2 create-key-pair --key-name RodApache --query 'KeyMaterial' --output text > RodApache.pem
    
#### Überprüfen

    ls -lah

#### Beschreibung von Keypair

    aws ec2 describe-key-pairs --key-name RodApache
    
#### Sicherheitsgruppe erstellen

    aws ec2 create-security-group --group-name RodApacheSG  --description "Allows SSH, HTTP, and HTTPS connections for the Web Server"
    
#### Sicherheitsgruppe-ID:
    
    sg-07902706288efc638

#### Regeln hinzufügen:

#### Port 22

    aws ec2 authorize-security-group-ingress --group-id sg-07902706288efc638 --protocol tcp --port 22 --cidr 0.0.0.0/0

#### Port 80

    aws ec2 authorize-security-group-ingress --group-id sg-07902706288efc638 --protocol tcp --port 80 --cidr 0.0.0.0/0

#### Port 443

    aws ec2 authorize-security-group-ingress --group-id sg-07902706288efc638 --protocol tcp --port 443 --cidr 0.0.0.0/0

#### Die für die Sicherheitsgruppe erstellten Regeln überprüfen

    aws ec2 describe-security-groups --group-ids sg-07902706288efc638

#### Bash-Script-Datei erstellen (webscript.sh)

    touch webscript.sh
    
#### EC2-Instanz erstellen
    
    aws ec2 run-instances --image-id ami-0b920b0594b5288fb --count 1 --instance-type t2.micro --key-name RodApache --security-group-ids sg-07902706288efc638 --user-data file://webscript.sh

#### Überprüfen

    aws ec2 describe-instances --filters Name=instance-state-name,Values=running | grep Rod
    
#### Berechtigungen der Datei ändern

    chmod 400 RodApache.pem

#### Verbindung per SSH mit der EC2-Instanz

    ssh -i RodApache.pem ec2-user@35.159.50.140
