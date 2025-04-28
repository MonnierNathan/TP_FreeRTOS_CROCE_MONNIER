# TP FreeRTos



## 0.1 Où se situe le fichier main.c?

Le fichier `main.c` se trouve dans le dossier Core/Srcdu projet généré par STM32CubeMX.

---

## 0.2 À quoi servent les commentaires indiquant BEGIN et END ?

Les commentaires de la forme **"USER CODE BEGIN"** et **"USER CODE END"** délimitent des zones réservées pour le code utilisateur.  
Ces sections permettent d’ajouter ou de modifier du code sans risquer de perdre les modifications lors d’une régénération du projet par STM32CubeMX.

---

## 0.3 Quels sont les paramètres à passer à `HAL_Delay` et `HAL_GPIO_TogglePin` ?

- **HAL_Delay** :  
  - **Paramètre** : Un entier représentant le nombre de millisecondes pendant lesquelles le programme doit rester en pause.  
  - **Exemple** : `HAL_Delay(500);` pour une pause de 500 ms.

- **HAL_GPIO_TogglePin** :
	- **Paramètre**:
		- Un pointeur vers le port GPIO
		- La broche sous forme de constante

---

## 0.4 Dans quel fichier les ports d’entrée/sorties sont-ils définis ?

Les ports d'entrée/sorties sont définis dans le fichier `main.h`.

---

## 0.5 Écrivez un programme simple permettant de faire clignoter la LED.
```c
while(1){
	HAL_GPIO_TogglePin(LD2_GPIO_PORT,LD2_Pin);
	HAL_Delay(500);
}
```	

---

## 0.6 Modifiez le programme pour que la LED s’allume lorsque le bouton USER est appuyé.
```c
while(1){
	if(Hal_GPIO_ReadPin(B1_GPIO_PORT,B1_Pin)){
		HAL_GPIO_WritePin(LD2_GPIO_PORT,LD2_Pin,'1');
	}
	else{
		HAL_GPIO_Write(LD2_GPIO_PORT,LD2_Pin,'0');
	}
	HAL_Delay(500);
}
```

---

## 1.1 Tâche simple

**1 En quoi le paramètre TOTAL_HEAP_SIZE a-t-il de l’importance ?**

TOTAL_HEAP_SIZE is the total amount of RAM available to the RTOS kernel. TOTAL_HEAP_SIZE setting has no effect unless heap_1.c, heap_2.c, heap_4.c or heap_5.c are being used by the application The value chosen by the user should take into account the application consumption of RAM otherwise it will lead to link issues!

**2  
	- Créez une tâche permettant de faire changer l’état de la LED toutes les 100ms
et profitez-en pour afficher du texte à chaque changement d’état.**

```c
#define DELAY_0 100

void CodeLedONOFF(void* p){

	int duree = (int) p;
	//char* s = pcTaskGetName(xTaskGetCurrentTaskHandle());
	while(1){
		HAL_GPIO_TogglePin(LD2_GPIO_Port, LD2_Pin);
		printf("ON OFF \n\r");
		vTaskDelay(pdMS_TO_TICKS(duree));
	}
}

int main(void)
{

  /* USER CODE BEGIN 1 */
	//xTaskCreate(TaskCode1);

	BaseType_t xReturned;
	TaskHandle_t xHandle1 = NULL;
	TaskHandle_t xHandle2 = NULL;


	xReturned = xTaskCreate(
		CodeLedONOFF, // Function that implements the task.
		"TaskCode0", // Text name for the task.
		STACK_SIZE, // Stack size in words, not bytes.
		(void *) DELAY_0, // Parameter passed into the task.
		1,//Priority at which the task is created.
		&xHandle1 ); // Used to pass out the created task's handle.
```

**- Quel est le rôle de la macro portTICK_PERIOD_MS ?**

La macro portTICK_PERIOD_MS dans FreeRTOS joue un rôle très important pour convertir les "ticks" du système en millisecondes. Elle sert à traduire la durée d’un tick du système en millisecondes, facilitant ainsi le travail avec des délais ou des temporisations en unités de temps humainement compréhensibles.

---

## 1.2 Sémaphores pour la synchronisation
**3 Créez deux tâches, taskGive et taskTake, ayant deux priorités differentes.
TaskGive donne un sémaphore toutes les 100ms. Affichez du texte avant et
après avoir donné le sémaphore. TaskTake prend le sémaphore. Affichez du
texte avant et après avoir pris le sémaphore.**

**4 Ajoutez un mécanisme de gestion d’erreur lors de l’acquisition du sémaphore.
On pourra par exemple invoquer un reset software au STM32 si le sémaphore
n’est pas acquis au bout d’une seconde.**

**5 Pour valider la gestion d’erreur, ajoutez 100ms au delai de TaskGive à chaque
itération.**

**6 Changez les priorités. Expliquez les changements dans l’affichage.**

---

## 1.3 Notification

**7 Modifiez le code pour obtenir le même fonctionnement en utilisant des task
notifications à la place des sémaphores.**

---

## 1.4 Queues

**8 Modifiez TaskGive pour envoyer dans une queue la valeur du timer. Modifiez
TaskTake pour réceptionner et afficher cette valeur.**

---

## 1.5 Réentrance et exclusion mutuelle

**10 Observez attentivement la sortie dans la console. Expliquez d’où vient le problème.**

**11 Proposez une solution en utilisant un sémaphore Mutex.**

---

## 2.1 Terminer l’intégration du shell commencé en TD. Pour mémoire, les questions du TD sont rappelées ci-dessous :
**1 Créer le projet, compiler et observer. Appeler la fonction depuis le shell.
*Les fichiers sont disponibles sur moodle, dans la section TD*.**

**2 Modifier la fonction pour faire apparaître la liste des arguments.**

**3 Expliquer les mécanismes qui mènent à l’exécution de la fonction.**

**4 Quel est le problème ?**

**5 Proposer une solution**

---

## 2.2 Que se passe-t-il si l’on ne respecte pas les priorités décrites précédemment ?

---

## 2.3 Écrire une fonction `led()`, appelable depuis le shell, permettant de faire clignoter la LED (PI1 sur la carte). Un paramètre de cette fonction configure la periode de clignotement. Une valeur de 0 maintient la LED éteinte.  Le clignotement de la LED s’effectue dans une tâche. Il faut donc trouver un moyen de faire communiquer *proprement* la fonction led avec la tâche de clignotement.

---

## 2.4 Écrire une fonction `spam()`, semblable à la fonction `led()` qui affiche du texte dans la liaison série au lieu de faire clignoter les LED. On peut ajouter comme argument le message à afficher et le nombre de valeurs à afficher. Ce genre de fonction peut être utile lorsque l’on travaille avec un capteur

---

## 3.1 Gestion du tas

**1. Quel est le nom de la zone réservée à l’allocation dynamique ?**

**2. Est-ce géré par FreeRTOS ou la HAL ?**

**3. Si ce n’est déjà fait, ajoutez de la gestion d’erreur sur toutes les fonctions
pouvant générer des erreurs. En cas d’erreur, affichez un message et appelez
la fonction `Error_Handler();`**

**4. Notez la mémoire RAM et Flash utilisée, comme dans l’exemple ci-dessous**

**5. Créez des tâches bidons jusqu’à avoir une erreur**

**6. Notez la nouvelle utilisation mémoire.**

**7. Dans CubeMX, augmentez la taille du tas (TOTAL_HEAP_SIZE). Générez le
code, compilez et testez.**

**8. Notez la nouvelle utilisation mémoire. Expliquez les trois relevés.**

---

## 3.2 Gestion des piles

**1. Lisez la doc suivante :
https://www.freertos.org/Stacks-and-stack-overflow-checking.html**

**2. Dans CubeMX, configurez CHECK_FOR_STACK_OVERFLOW**

**3. Écrivez la fonction `vApplicationStackOverflowHook`. (Rappel : C’est une
fonction appelée automatiquement par FreeRTOS, vous n’avez pas à l’appeler
vous-même).**

**4. Débrouillez vous pour remplir la pile d’une tâche pour tester. Notez que, vu
le contexte d’erreur, il ne sera peut-être pas possible de faire grand chose
dans cette fonction. Utilisez le debugger.**

**5. Il existe d’autres hooks. Expliquez l’intérêt de chacun d’entre eux.**

---

## 3.3 Statistiques dans l’IDE

**1. Dans CubeMX, activez les trois paramètres suivants :
	- GENERATE_RUN_TIME_STATS
	- USE_TRACE_FACILITY
	- USE_STATS_FORMATTING_FUNCTIONS**

**2. Générez le code, compilez et lancez en mode debug**

**3. Pour ajouter les statistiques, cliquez sur Window > Show View > FreeRTOS> FreeRTOS Task List. Vous pouvez aussi afficher les queues et les sémaphores.**

**4. Lancez le programme puis mettez-le en pause pour voir les statistiques.**

**5. Cherchez dans CubeMX comment faire pour afficher l’utilisation de la pile.
En mode debug, cliquez sur Toggle Stack Checking (dans l’onglet FreeRTOSTask List en haut à droite).**

**6. Pour afficher le taux d’utilisation du CPU, il faut écrire les deux fonctions suivantes :
`void configureTimerForRunTimeStats(void);`
`unsigned long getRunTimeCounterValue(void);`
La première fonction doit démarrer un timer, la seconde permet de récupérer
la valeur du timer. Si vous utilisez un timer 16 bits, il faudra peut-être bricoler
un peu.
Encore une fois, ce sont des hooks, elles sont donc automatiquement appelées par l’OS.**

**7. Affichez les sémaphores et les queues.**

**8. Si vous n’en utilisez pas dans votre projet, créez deux tâches qui se partagent
une queue ou un sémaphore.**

**9. Pour leur donner un nom compréhensible, utilisez la fonction
vQueueAddToRegistry.**

---

## 3.4 Affichage des statistiques dans le shell

**Écrire une fonction appelable depuis le shell pour afficher les statistiques
dans le terminal.**

---

## 4.1 Interfacer l’ADXL345

**1. Dans le fichier ioc, configurez les pins suivantes :
	- PB14 : SPI2_MISO
	- PB15 : SPI2_MOSI
	- PI1 : SPI2_SCK
	- PG7 : GPIO_EXTI7 (nommez le INT)
	- PB4 : GPIO_Output (nommez le NSS), doit être à High par défaut.**

**2. Configurez le SPI2 en Mode Full-Duplex Master, puis configurez :
	- Frame Format : Motorola
	- Clock Polarity : High
	- Clock Phase : 2 Edge
	- CRC Calculation : Disabled
	- NSS Signal Type : Software
	- Prescaler : Valeur permettant d’avoir un Baud Rate compatible avec le
composant (voir datasheet ADXL345)
	- Pour le reste, voir la datasheet.**

**3. Câblez le composant sur la carte Discovery :
	- VCC sur le 5V
	- GND sur GND
	- CS sur D3
	- SDO sur D12
	- SDA sur D11
	- SCL sur D13
	- INT1 sur D4
	- INT2 non connectée.**

---

## 4.2 Premiers tests

**1. Créez une fonction appelable depuis le shell pour faire vos tests.
Le registre DEVID (adresse 0x00) est une constante qui permet de tester la communication SPI. Pour lire un registre, il faut d’abord écrire l’adresse, puis lire la
valeur, dans la même trame SPI. Inspirez vous de l’exemple ci-dessous.
`HAL_GPIO_WritePin(NSS_GPIO_Port, NSS_Pin, GPIO_PIN_RESET);`
`HAL_SPI_Transmit(&hspi2, &address, 1, HAL_MAX_DELAY);`
`HAL_SPI_Receive(&hspi2, p_data, size, HAL_MAX_DELAY);`
`HAL_GPIO_WritePin(NSS_GPIO_Port, NSS_Pin, GPIO_PIN_SET);`**

**2. Dans la fonction shell, écrivez un code permettant de récupérer la valeur du
DEVID, et vérifiez si elle est correcte.**

**3. Quelles sont les valeurs à mettre dans les registres INT_ENABLE et POWER_CTL
pour démarrer la mesure et délencher une interruption à chaque mesure ?**

**4. À la suite du code précédent, dans la fonction shell, écrivez un code permettant de lire 4 valeurs consécutives. Utilisez du polling pour attendre que la
broche INT1 passe à High.**

**5. Faites la moyenne de ces quatre valeurs, mettez les accélérations en forme,
et affichez-les à travers l’UART.**

---

## 4.3 Driver SPI

**1. Créez un dossier adxl345 à la racine.**

**2. Ajoutez le dossier au path (Click droit > Add/Remove include path...)**

**3. Project > Properties, puis C/C++ General > Paths and Symbols > Source
Location, cliquez sur Add Folder... et choissisez le dossier adxl345.**

**4. Dans ce dossier, créez deux fichiers drv_spi.c et drv_spi.h**

**5. Créez également deux fichiers adxl345.c et adxl345.h**

**6. Dans drv_spi.h, écrivez le prototype des trois fonctions suivantes :
`int drv_spi_init(void);`
`int drv_spi_write(uint8_t address, uint8_t * p_data, uint16_t size);`
`int drv_spi_read(uint8_t address, uint8_t * p_data, uint16_t size);`**

**7. Écrivez le contenu des fonctions dans le fichier drv_spi.c. La fonction d’initialisation ne fait rien pour l’instant. Les fonctions read et write utilisent les
fonctions de la HAL. Pour l’instant vous n’utiliserez pas d’interruption.**

**8. Testez le driver dans le code précédent.**

---
## Les étudiants
- CROCE Léonard
- MONNIER Nathan
