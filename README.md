# connection-adruino-camera
Programmer la camera pour Arduino uno
La programmation d'une caméra ArduCAM OV7670 avec un Arduino repose sur l'utilisation de plusieurs protocoles de communication et bibliothèques spécifiques. Ce code illustre les étapes essentielles pour initialiser la caméra et capturer des images.

1. Présentation de la caméra OV7670
L'OV7670 est un capteur d'image CMOS capable de capturer des images en couleur avec une résolution maximale de 640x480 pixels (VGA). Il communique avec un microcontrôleur comme l'Arduino via le protocole I2C pour la configuration et SPI pour le transfert des données.

2. Bibliothèques et Configuration du SPI et I2C
Le code commence par l’inclusion des bibliothèques nécessaires :

cpp
Copier
Modifier
#include <Wire.h>     // Pour la communication I2C
#include <ArduCAM.h>  // Bibliothèque ArduCAM pour gérer la caméra
#include <SPI.h>      // Pour la communication SPI
Wire.h : utilisée pour la communication I2C, permettant de configurer les registres de la caméra.
ArduCAM.h : fournit les fonctions spécifiques pour le contrôle de la caméra.
SPI.h : permet d’envoyer et recevoir des données en SPI avec la caméra.
3. Définition et Initialisation de la Caméra
La caméra est connectée via une broche CS (Chip Select), définie ici :

cpp
Copier
Modifier
#define CS_PIN 10  // Broche CS pour la communication SPI avec la caméra
ArduCAM myCAM(OV7670, CS_PIN);
L’objet myCAM est créé en utilisant la classe ArduCAM, spécifiant le modèle OV7670 et la broche CS_PIN.

4. Configuration et Initialisation de la Caméra
Dans la fonction setup(), les étapes suivantes sont effectuées :

cpp
Copier
Modifier
void setup() {
  Serial.begin(115200);
  Wire.begin();
  
  pinMode(CS_PIN, OUTPUT);
  digitalWrite(CS_PIN, HIGH);
Initialisation du port série pour afficher les messages de diagnostic.
Démarrage de la communication I2C avec Wire.begin().
Configuration de la broche CS en sortie pour contrôler la caméra.
Ensuite, la caméra est réinitialisée :

cpp
Copier
Modifier
  myCAM.write_reg(0x12, 0x80);  // Reset de la caméra
  delay(100);
  
  Serial.println("Initialisation de la caméra OV7670...");
}
L’instruction write_reg(0x12, 0x80) envoie une commande pour réinitialiser les registres de la caméra à leurs valeurs par défaut.

5. Capture d'une Image
Dans la fonction loop(), l'Arduino effectue une capture d'image en suivant ces étapes :

a) Préparation de la capture
cpp
Copier
Modifier
  Serial.println("Capture d'image...");
  
  myCAM.flush_fifo();
  myCAM.clear_fifo_flag();
  myCAM.start_capture();
flush_fifo() : vide la mémoire tampon de la caméra.
clear_fifo_flag() : réinitialise les indicateurs de capture.
start_capture() : démarre la capture d’une nouvelle image.
b) Attente de la fin de la capture
cpp
Copier
Modifier
  while (!myCAM.get_bit(ARDUCHIP_TRIG, CAP_DONE_MASK));
L'Arduino attend ici que la capture soit terminée en vérifiant un registre spécifique.

c) Fin de la capture
cpp
Copier
Modifier
  Serial.println("Image capturée !");
  delay(1000); // Pause entre chaque capture
Une fois l’image capturée, un message est affiché sur le port série, puis l’Arduino attend 1 seconde avant de recommencer le processus.

6. Améliorations possibles
Ajouter une carte SD pour enregistrer les images.
Envoyer les images à un PC ou un serveur via Wi-Fi ou Bluetooth.
Modifier la résolution ou d'autres paramètres de la caméra via les registres.
Conclusion
Ce programme montre comment initialiser et capturer une image avec une caméra OV7670 en utilisant un Arduino et la bibliothèque ArduCAM. Bien que ce code ne récupère pas encore les données de l’image, il constitue une base pour des projets avancés en traitement d’image embarqué.
