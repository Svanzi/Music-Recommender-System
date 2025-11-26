1. Si parte da due dataset 
    - `plays` → Artisti ascoltati per ogni utente
    - `artists` → Artisti del dataset, ascoltati e taggati dagli utenti
2. Viene creato un nuovo DataFrame chiamato `ap`che contiene, per ogni ascolto registrato, sia i dettagli dell'utente e del conteggio (`userID`, `weight`) sia i dettagli dell'artista corrispondente (`id`, `name`, ecc.).
3. Vengono fatti due ranking sfruttando `ap` :
    - `artist_rank` → classifica degli artisti più popolari (più ascoltati in totale) e quanto sono amati dai loro utenti (ascolti medi per utente)
      
        <img width="488" height="201" alt="Screenshot 2025-11-26 151058" src="https://github.com/user-attachments/assets/d18a6687-5868-4aa1-9766-41e2e79560ee" />
        
    - `user_rank` → classifica degli utenti più attivi sulla piattaforma, sia in termini di varietà di artisti ascoltati che di numero totale di ascolti.
      
        <img width="324" height="196" alt="Screenshot 2025-11-26 151105" src="https://github.com/user-attachments/assets/079713fe-8642-4092-bffb-23f8f336096c" />

1. Vengono creati due nuovi DataFrame 
    - `ap2` → aggiunge ad ogni riga di `ap` informazioni dell’artista riguardo al suo `artist_rank`
        - Se una riga in `ap` rappresenta "l'utente X ha ascoltato Lady Gaga 50 volte", questa riga in `ap2` conterrà anche le informazioni globali su Lady Gaga, come "Lady Gaga è stata ascoltata in totale 1,000,000 di volte da 5,000 utenti".
          
          <img width="757" height="169" alt="Screenshot 2025-11-26 151613" src="https://github.com/user-attachments/assets/32a76e06-6950-4143-a043-50ad7dc3d1c8" />
    
    - `ap3` → ogni riga di questa tabella contiene:
        - Quante volte l’utente X ha ascoltato l’artista Y
        - I dati aggregati sull'artista Y (popolarità totale, ecc.).
        - I dati aggregati sull'utente X (attività totale, ecc.).
        - Viene ordinato in modo tale da mette in cima le interazioni utente-artista con il maggior numero di ascolti in assoluto.

          <img width="1050" height="164" alt="Screenshot 2025-11-26 151333" src="https://github.com/user-attachments/assets/32cca67b-c592-4746-a249-2fb8f637bf67" />
    
2. Parte di visualizzazione dei dati:
    - Ascolti totali per artista
    - Ascoltatori unici per artista
    - Top 20 di artisti e utenti
    - Popolarità degli artisti
3. Costruzione del sistema di raccomandazione basato sul item-based collaborative-filtering:
    - Costruzione della matrice di sparsità:
        - ogni riga rappresenta un artista unico e ogni colonna un utente unico.
        - Il valore in ogni cella `(i, j)` corrisponde al numero di volte (`userArtistPlays`) che l'utente `j` ha ascoltato l'artista `i`.
        - Le interazioni non esistenti vengono riempite con il valore `0`.
        - Per ottimizzare l'uso della memoria e l'efficienza computazionale, la matrice viene convertita in un formato compresso (Compressed Sparse Row).
4. Viene utilizzato un modello `K-Nearest Neighbors` (KNN) per calcolare la similarità tra gli artisti utilizzando la metrica coseno
5. Viene utilizzata una funzione `fuzzy matching` per evitare problemi dovuti ad errori di battitura dell’utente.
6. Vengono applicati poi i seguenti passaggi per estrarre le raccomandazioni in base all’input dell’utente:
    - Viene ricevuta la query dell'utente come stringa.
    - Viene usata la funzione fuzzy per trovare il nome dell'artista più simile nel dataset e il suo indice numerico.
    - L’indice numerico, insieme al modello e al numero di raccomandazioni desiderate (`k`), viene passo alla funzione `idx_recommend.`
    - `idx_recommend` si occupa di interrogare il modello `knn`e di stampare i risultati finali.
7. Viene migliorata la qualità e l’affidabilità delle raccomandazioni eliminando utenti poco attivi e artisti poco popolari.
    - Viene creato un nuovo DataFrame `ap4` :
        - Vengono selezionate solo le righe di `ap3` che presentano:
            1. **Utenti sufficientemente attivi** (in termini di ascolti totali e varietà di artisti).
            2. **Artisti sufficientemente popolari** (in termini di ascolti totali e numero di ascoltatori unici).
    - Vengono mostrate le dimensioni e le statistiche del dataset prima e dopo il filtraggio per valutare l'impatto delle soglie scelte e quanti dati vengono scartati.
8. Viene ripetuto l’intero processo di preparazione e addestramento del modello con `ap4` :
    - Viene creata una nuova matrice si sparsità
    - Viene creato un novo modello `knn`
9. Vengono ristampati i risultati della raccomandazionecon una lista di artisti di prova
