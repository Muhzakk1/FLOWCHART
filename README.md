```mermaid
graph TD
    A[START] --> B{SETUP};
    
    %% Setup Section
    B --> C(Inisialisasi Serial 11520);
    C --> D(Inisialisasi Sensor DHT);
    D --> E(Atur PIN_LED sebagai OUTPUT);
    E --> F(Atur PIN_HUJAN sebagai INPUT);
    F --> G(Lampirkan Servo ke PIN_SERVO);
    G --> H(Atur posisi awal Servo ke posisiBuka 100);
    H --> I(Koneksi ke WiFi & Blynk);
    I --> J(Set Timer untuk cekSuhu);
    J --> K(Set Timer untuk cekHujan);
    K --> L(Tampilkan Sistem jemuran otomatis siap);

    %% Loop Section
    L --> M{LOOP};
    M --> N(Jalankan cekHujan);
    M --> O(Jalankan cekSuhu);
    N --> M;
    O --> M;

    %% cekHujan Function
    subgraph cekHujan
        P[START] --> Q(rainValue = BACA_DIGITAL PIN_HUJAN);
        Q --> R{rainValue SAMA DENGAN LOW?};
        
        R -- YA (Hujan) --> S(myServo.write posisiTutup);
        S --> T(Tampilkan "HUJAN → tutup jemuran." di Serial);
        T --> U(ATUR_DIGITAL(PIN_LED, HIGH));
        U --> V(Kirim ke Blynk V3: "Sedang Hujan");
        V --> W(Kirim ke Blynk V4: 255);
        W --> X[END];
        
        R -- TIDAK (Cerah) --> Y(myServo.write posisiBuka);
        Y --> Z(Tampilkan "CERAH = Buka jemuran" di Serial);
        Z --> AA(ATUR_DIGITAL(PIN_LED, LOW));
        AA --> BB(Kirim ke Blynk V3: "Tidak Hujan");
        BB --> CC(Kirim ke Blynk V4: 0);
        CC --> X;
    end
    
    %% cekSuhu Function
    subgraph cekSuhu()
        DD[START] --> EE(suhu = BACA_SUHU_CELSIUS DHTPIN );
        EE --> FF(lembab = BACA_KELEMBAPAN DHTPIN);
        FF --> GG{suhu ADALAH NaN ATAU lembab ADALAH NaN?};
        
        GG -- YA --> HH(Tampilkan "Gagal membaca sensor!" di Serial);
        HH --> II[KELUAR DARI FUNGSI/END];

        GG -- TIDAK --> JJ(Tampilkan "Suhu: [suhu] °C, Kelembapan: [lembab] %" di Serial);
        JJ --> KK(Kirim ke Blynk V0: suhu);
        KK --> LL(Kirim ke Blynk V1: lembab);
        LL --> MM(TUNDA);
        MM --> II;
    end
