# db-hotel


## Query base
- Seleziona tutti gli ospiti che sono stati identificati con la carta di identità
```
SELECT * 
FROM `ospiti`
WHERE `document_type` = 'CI';
```

- Seleziona tutti gli ospiti che sono nati dopo il 1988
```
SELECT * 
FROM `ospiti`
WHERE YEAR(`date_of_birth`) > '1988';
```

- Seleziona tutti gli ospiti che hanno più di 20 anni (al momento dell’esecuzione della query)
```
SELECT * 
FROM `ospiti`
WHERE `date_of_birth` <= '2000-05';
```

- Seleziona tutti gli ospiti il cui nome inizia con la D
```
SELECT * 
FROM `ospiti`
WHERE `name` LIKE 'D%';
```
- Calcola il totale degli ordini accepted
```
SELECT COUNT(`id`) as `totale accettati`, `status`
FROM `pagamenti`
WHERE `status` = 'accepted'
```

- Qual è il prezzo massimo pagato?
```
SELECT `status`, MAX(`price`)
FROM `pagamenti`
WHERE `status` = 'accepted'
GROUP BY `status`;
```

- Seleziona gli ospiti riconosciuti con patente e nati nel 1975
```
SELECT * 
FROM `ospiti` 
WHERE `document_type` = 'Driver License' 
AND `date_of_birth` BETWEEN '1975-01-01' AND '1975-12-31';
```
- Quanti posti letto ha l’hotel in totale?
```
SELECT SUM(`beds`) 
FROM `stanze`;
```

## Group by

- Conta gli ospiti raggruppandoli per anno di nascita
```
SELECT COUNT(`id`), YEAR(`date_of_birth`)
FROM `ospiti`
GROUP BY YEAR(`date_of_birth`);
```
- Somma i prezzi dei pagamenti raggruppandoli per status
```
SELECT `status`, SUM(`price`) as `totale`
FROM `pagamenti`
GROUP BY `status`;
```
- Conta quante volte è stata prenotata ogni stanza
```
SELECT `stanza_id`, COUNT(`id`) as `numero_prenotazioni`
FROM `prenotazioni`
GROUP BY `stanza_id`;
```
- Fai una analisi per vedere se ci sono ore in cui le prenotazioni sono più frequenti
```
SELECT HOUR(`created_at`) as `ora_della_prenotazione`, COUNT(`id`) as `numero_prenotazioni`
FROM `prenotazioni`
GROUP BY HOUR(`created_at`)
ORDER BY COUNT(`id`) DESC;
```

- Quante prenotazioni ha fatto l’ospite che ha fatto più prenotazioni?
```
SELECT `ospite_id`, COUNT(`ospite_id`) as `numero_prenotazioni`
FROM `prenotazioni_has_ospiti`
GROUP BY `ospite_id`
ORDER by COUNT(`ospite_id`) DESC
LIMIT 1;
```

## Join

- Come si chiamano gli ospiti che hanno fatto più di due prenotazioni?
```
SELECT `ospite_id`, COUNT(`ospite_id`) as `numero_prenotazioni`, `ospiti`.`name`, `ospiti`.`lastname`
FROM `prenotazioni_has_ospiti`
INNER JOIN `ospiti` ON `prenotazioni_has_ospiti`.`ospite_id` = `ospiti`.`id`
GROUP BY `ospite_id`
HAVING COUNT(`ospite_id`) > 2
ORDER BY COUNT(`ospite_id`) DESC;
```
- Stampare tutti gli ospiti per ogni prenotazione
```
SELECT `prenotazioni_has_ospiti`.`prenotazione_id`, `ospiti`.`id` as `ospiti_id`, `ospiti`.`name`, `ospiti`.`lastname`
FROM `prenotazioni_has_ospiti`
INNER JOIN `ospiti` ON `prenotazioni_has_ospiti`.`ospite_id` = `ospiti`.`id`;
```
- Stampare Nome, Cognome, Prezzo e Pagante per tutte le prenotazioni fatte a Maggio 2018
```
SELECT `prenotazioni_has_ospiti`.`id`, `ospiti`.`name`, `ospiti`.`lastname`, `pagamenti`.`price`, `paganti`.`name` as `name_pagante`, `paganti`.`lastname` as `lastname_pagante`, `prenotazioni_has_ospiti`.`created_at`
FROM `prenotazioni_has_ospiti`
INNER JOIN `ospiti` ON `prenotazioni_has_ospiti`.`ospite_id` = `ospiti`.`id`
INNER JOIN `pagamenti` ON `prenotazioni_has_ospiti`.`prenotazione_id` = `pagamenti`.`prenotazione_id`
INNER JOIN `paganti` ON `pagamenti`.`pagante_id` = `paganti`.`id`
WHERE YEAR(`prenotazioni_has_ospiti`.`created_at`) = '2018' AND MONTH(`prenotazioni_has_ospiti`.`created_at`) = '05';
```
- Fai la somma di tutti i prezzi delle prenotazioni per le stanze del primo piano
```
SELECT SUM(`pagamenti`.`price`) 
FROM `stanze` 
INNER JOIN `prenotazioni` ON `stanze`.`id` = `prenotazioni`.`stanza_id`
INNER JOIN `pagamenti` ON `prenotazioni`.`id` = `pagamenti`.`prenotazione_id`
WHERE `stanze`.`floor` = '1';
```
- Prendi i dati di fatturazione per la prenotazione con id=7
```
SELECT `prenotazioni`.`id` as 'prenotazione_id', `paganti`.`name`, `paganti`.`lastname`, `paganti`.`address`, `pagamenti`.`price`
FROM `prenotazioni`
INNER JOIN `pagamenti` ON `prenotazioni`.`id` = `pagamenti`.`prenotazione_id`
INNER JOIN `paganti` ON `pagamenti`.`pagante_id` = `paganti`.`id`
WHERE `prenotazioni`.`id` = '7';
```
- Le stanze sono state tutte prenotate almeno una volta? (Visualizzare le stanze non ancora prenotate)
```
SELECT `stanze`.`room_number` 
FROM `stanze`
LEFT JOIN `prenotazioni` ON `stanze`.`id` = `prenotazioni`.`stanza_id`
WHERE ISNULL(`prenotazioni`.`stanza_id`);
```
