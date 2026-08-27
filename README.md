# Caso de Estudio / Case Study / Étude de Cas

Selecciona tu idioma / Select your language / Choisissez votre langue :

<details>
<summary><b>Español (Haga clic para desplegar)</b></summary>

### 1. Resumen del problema y la solución
*   **Problema:** Pérdida de acceso lógico a una estación de trabajo Ubuntu Linux debido al olvido de las credenciales de autenticación del usuario local.
*   **Solución:** Interrupción limpia del gestor de arranque mediante hardware, acceso al entorno de recuperación del sistema para obtener una consola con privilegios de administrador (root), diagnóstico de la estructura de almacenamiento y reescritura de las credenciales en caliente.

---

### 2. Equipos utilizados
*   **Hardware base:** Apple Mac (Legacy) configurado con arquitectura nativa Linux.
*   **Sistema Operativo Objetivo:** Ubuntu Linux (Kernel 6.17.0-19-generic).
*   **Vector de Intervención:** Acceso físico local (Physical Access Vector).

---

### 3. Análisis de ejecución paso a paso y evidencias visuales

#### Paso 1: Interrupción del Bootloader mediante Hardware
Durante el ciclo de encendido del hardware, se ejecutó una pulsación única de la tecla ESC en el momento exacto del arranque del firmware. Esta acción interrumpió el inicio automatizado estándar, cargando de manera exitosa la interfaz principal del gestor de arranque GNU GRUB (versión 2.12).

<p align="center"><img src="1.jpeg" width="70%" alt="Menú principal de GNU GRUB"></p>

#### Paso 2: Escalada de Privilegios mediante el Modo de Recuperación
Dentro de las opciones de arranque, se seleccionó la ruta "Advanced options for Ubuntu" y se ejecutó la imagen del núcleo terminada en la versión 19 bajo el perfil "(recovery mode)". Al desplegarse el menú de mantenimiento del sistema, se interceptó la seguridad lógica seleccionando la opción "root", lo que otorgó acceso directo a una consola interactiva de superusuario (root@thomas-MacBookPro8-1:~#) tras presionar la tecla Enter.

<p align="center"><img src="4.jpeg" width="70%" alt="Menú de recuperación de Ubuntu e inicio de consola root"></p>

#### Paso 3: Auditoría del Sistema de Archivos, Corrección de Errores y Mitigación
Al intentar remontar el directorio raíz con permisos de escritura, el sistema operativo denegó la operación reportando un fallo por "tipo de sistema de ficheros incorrecto". 

Para diagnosticar el estado del almacenamiento, se ejecutó un comando de reconocimiento analítico:
```bash
lsblk -f
```
La salida en pantalla confirmó que la partición del sistema operativo residía correctamente en `/dev/sda1` bajo formato `ext4`. El análisis del historial reveló que el fallo inicial no era del sistema, sino un error de sintaxis en el comando previo (se había tipeado "renmount" en lugar del parámetro legítimo "remount"). 

Una vez identificado el problema, se ejecutó correctamente la instrucción de montaje:
```bash
mount -o remount,rw /
```
Con el sistema de archivos desbloqueado y en modo lectura/escritura, se verificó el usuario en `/home` y se procedió a la reescritura de la credencial afectada mediante la utilidad del sistema:
```bash
passwd thomas
```
Tras corregir las alertas por longitud de caracteres, el sistema confirmó la actualización exitosa de los hashes de seguridad en los archivos internos de credenciales ("contraseña actualizada correctamente"). El incidente se cerró aplicando un reinicio limpio con el comando `reboot`.

<p align="center"><img src="8.jpeg" width="80%" alt="Diagnóstico de discos con lsblk y actualización exitosa de contraseña"></p>

---

### 4. Conclusión de seguridad
Este ejercicio demuestra de forma práctica un axioma central de la seguridad informática: si un vector de ataque obtiene acceso físico directo al teclado de una máquina cuyo almacenamiento local no se encuentra cifrado, los controles de autenticación visual del sistema operativo quedan completamente anulados. Las herramientas de recuperación nativas pueden ser utilizadas para comprometer la integridad de las cuentas de usuario en pocos minutos.

### 5. Contramedidas recomendadas para producción
1.  **Cifrado de Disco Completo (LUKS):** Asegura que los datos del sistema de archivos permanezcan confidenciales e ilegibles incluso si se obtiene acceso a una consola de Root por la fuerza.
2.  **Protección Criptográfica del GRUB:** Configurar una contraseña en el cargador de arranque para bloquear el acceso al modo de recuperación y a las opciones avanzadas del sistema.

</details>

<details>
<summary><b>English (Click to expand)</b></summary>

### 1. Problem and Solution Summary
*   **Problem:** Loss of logical access to an Ubuntu Linux workstation due to forgotten local user authentication credentials.
*   **Solution:** Clean bootloader interruption via hardware, accessing the system recovery environment to obtain a root administrator shell, diagnosing the storage structure, and hot-overwriting the account credentials.

---

### 2. Equipment Used
*   **Base Hardware:** Apple Mac (Legacy) configured with native Linux architecture.
*   **Target OS:** Ubuntu Linux (Kernel 6.17.0-19-generic).
*   **Intervention Vector:** Local Physical Access Vector.

---

### 3. Step-by-Step Execution Analysis and Real Photos

#### Step 1: Hardware-Based Bootloader Interruption
During the hardware power cycle, a single press of the ESC key was executed at the exact moment of firmware initialization. This action successfully interrupted the standard automated boot process, loading the GNU GRUB bootloader main interface (version 2.12).

<p align="center"><img src="1.jpeg" width="70%" alt="GNU GRUB main menu"></p>

#### Step 2: Privilege Escalation via Recovery Mode
Within the boot options, the "Advanced options for Ubuntu" path was selected, and the kernel image ending in version 19 was executed under the "(recovery mode)" profile. Upon deployment of the system maintenance menu, logical security was bypassed by selecting the "root" option, granting direct access to an interactive superuser shell (root@thomas-MacBookPro8-1:~#) after pressing Enter.

<p align="center"><img src="4.jpeg" width="70%" alt="Ubuntu recovery menu and root shell initiation"></p>

#### Step 3: Filesystem Audit, Troubleshooting, and Mitigation
When attempting to remount the root directory with write permissions, the operating system denied the operation, reporting a failure due to a "wrong filesystem type".

To diagnose the storage state, an analytical discovery command was executed:
```bash
lsblk -f
```
The screen output confirmed that the operating system partition resided correctly on `/dev/sda1` under the `ext4` format. Analysis of the command history revealed that the initial failure was not a system error, but rather a syntax mistake in the previous command (the word "renmount" had been typed instead of the legitimate "remount" parameter).

Once the issue was identified, the mount instruction was successfully re-executed:
```bash
mount -o remount,rw /
```
With the filesystem unlocked in read/write mode, the user directory was verified under `/home`, and the affected credential was overwritten using the system utility:
```bash
passwd thomas
```
After resolving the character length policy warnings, the system confirmed the successful update of the security hashes within the internal credential files ("contraseña actualizada correctamente"). The incident was resolved by executing a clean system restart using the `reboot` command.

<p align="center"><img src="8.jpeg" width="80%" alt="Disk diagnosis with lsblk and successful password update"></p>

---

### 4. Security Conclusion
This exercise practically demonstrates a core axiom of information security: if an attack vector gains direct physical access to the keyboard of a machine whose local storage is unencrypted, the operating system's visual authentication controls are rendered entirely null. Native recovery tools can be leveraged to compromise the integrity of user accounts within minutes.

### 5. Recommended Production Countermeasures
1.  **Full Disk Encryption (LUKS):** Ensures that filesystem data remains confidential and unreadable even if root shell access is forced.
2.  **GRUB Cryptographic Protection:** Configure a password within the bootloader to lock unauthorized access to recovery mode and advanced system options.

</details>

<details>
<summary><b>Français (Cliquez pour dérouler)</b></summary>

### 1. Résumé du problème et de la solution
*   **Problème :** Perte d'accès logique à une station de travail Ubuntu Linux suite à l'oubli des identifiants d'authentification de l'utilisateur local.
*   **Solution :** Interruption propre du chargeur d'amorçage par le matériel, accès à l'environnement de récupération du système pour obtenir une console avec privilèges d'administrateur (root), diagnostic de la structure de stockage et réécriture des identifiants à chaud.

---

### 2. Équipement utilisé
*   **Matériel de base :** Apple Mac (Legacy) configuré avec une architecture native Linux.
*   **Système d'Exploitation Cible :** Ubuntu Linux (Noyau 6.17.0-19-generic).
*   **Vecteur d'Intervention :** Accès physique local (Physical Access Vector).

---

### 3. Actions étape par étape et photos réelles

#### Étape 1 : Interruption du Bootloader par le Matériel
Pendant le cycle de démarrage du matériel, une pression unique sur la touche ESC a été exécutée au moment exact de l'initialisation du firmware. Cette action a interrompu le processus de démarrage automatisé standard, chargeant avec succès l'interface principale du chargeur d'amorçage GNU GRUB (version 2.12).

<p align="center"><img src="1.jpeg" width="70%" alt="Menu principal de GNU GRUB"></p>


#### Étape 2 : Élévation de Privilèges via le Mode de Récupération
Parmi les options de démarrage, le chemin "Advanced options for Ubuntu" a été sélectionné et l'image du noyau se terminant par la version 19 a été exécutée sous le profil "(recovery mode)". Lors du déploiement du menu de maintenance du système, la sécurité logique a été contournée en sélectionnant l'option "root", ce qui a octroyé un accès direct à une console interactive de superutilisateur (root@thomas-MacBookPro8-1:~#) après avoir appuyé sur la touche Entrée.

<p align="center"><img src="4.jpeg" width="70%" alt="Menu de récupération Ubuntu"></p>

#### Étape 3 : Audit du Système de Fichiers, Résolution de Problèmes et Atténuation
Lors de la tentative de remontage du répertoire racine avec les permissions d'écriture, le système d'exploitation a refusé l'opération, signalant un échec pour "système de fichiers incorrect".

Pour diagnostiquer l'état du stockage, une commande de reconnaissance analytique a été exécutée :
```bash
lsblk -f
```

La sortie à l'écran a confirmé que la partition du système d'exploitation résidait correctement sur `/dev/sda1` sous le format `ext4`. L'analyse de l'historique des commandes a révélé que l'échec initial n'était pas une erreur système, mais une faute de syntaxe dans la commande précédente (le mot "renmount" avait été saisi à la place du paramètre légitime "remount").

Une fois le problème identifié, l'instruction de montage a été correctement réexécutée :
```bash
mount -o remount,rw /
```

Le système de fichiers étant déverrouillé en mode lecture/écriture, le dossier de l'utilisateur a été vérifié sous `/home`, et l'identifiant concerné a été réécrit via l'utilitaire système :
```bash
passwd thomas
```

Après avoir résolu les avertissements liés à la politique de longueur des caractères, le système a confirmé la mise à jour réussie des empreintes de sécurité dans les fichiers internes des identifiants ("contraseña actualizada correctement"). L'incident a été résolu en appliquant un redémarrage propre avec la commande `reboot`.

<p align="center"><img src="8.jpeg" width="80%" alt="Changement de mot de passe réussi"></p>

---

### 4. Conclusion de sécurité
Cet exercice démontre de manière pratique un axiome central de la sécurité informatique : si un vecteur d'attaque obtient un accès physique direct au clavier d'une machine dont le stockage local n'est pas chiffré, les contrôles d'authentification visuelle du système d'exploitation sont entièrement annulés. Les outils de récupération natifs peuvent être exploités pour compromettre l'intégrité des comptes d'utilisateurs en quelques minutes.

### 5. Contremesures recommandées pour les environnements réels
1. **Chiffrement Intégral du Disque (LUKS) :** Assure que les données du système de fichiers restent confidentielles et illisibles même si l'accès à la console Root est obtenu par la force.
2. **Protection Cryptographique du GRUB :** Configurer un mot de passe dans le chargeur d'amorçage pour bloquer l'accès non autorisé au mode de récupération et aux options avancées du système.


