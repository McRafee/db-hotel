## db-hotel


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
WHERE `date_of_birth` > '1988-12-31';
```

- Seleziona tutti gli ospiti che hanno più di 20 anni (al momento dell’esecuzione della query)
```
SELECT * 
FROM `ospiti`
WHERE `date_of_birth` < '2000-05-12';
```

- Seleziona tutti gli ospiti il cui nome inizia con la D
```
SELECT * 
FROM `ospiti`
WHERE `name` LIKE 'D%';
```
- Calcola il totale degli ordini accepted
```
SELECT `status`, SUM(price)
FROM `pagamenti`
WHERE `status` = 'accepted'
GROUP BY `status`;
```

- Qual è il prezzo massimo pagato?
```
SELECT `status`, MAX(price)
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
