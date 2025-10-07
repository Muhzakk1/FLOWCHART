```mermaid
graph TD
    A[START] --> B{SETUP};
    
    %% Setup Section
    B --> C(Inisialisasi Servo ke Posisi Buka: 100);
    C --> D(Mulai Sensor DHT);
    D --> E(Hubungkan Servo ke pin 23);
    E --> F(Atur pin Rain Sensor 27 sebagai INPUT);
    F --> G(Atur pin LED 21 sebagai OUTPUT);
    G --> H(Koneksi ke WiFi & Blynk dengan AUTH_TOKEN);
    H --> I(Set Timer 2000ms untuk cekSuhu);
    I --> J(Set Timer 2000ms untuk cekHujan);
    J --> K(Serial Print: Sistem siap...);

    %% Loop Section
    K --> L{LOOP};
    L --> M(Panggil cekHujan());
    L --> N(Panggil cekSuhu());
    M --> L;
    N --> L;

    %% cekHujan Function
    subgraph cekHujan()
        O[START] --> P(Baca nilai digital dari Rain Sensor (rainPin 27));
        P --> Q{Apakah rainValue == LOW?};
        
        Q -- YA (Hujan) --> R(Tulis Servo ke posisi Tutup: 0);
        R --> S(Serial Print: HUJAN → tutup jemuran);
        S --> T(Nyala LED Fisik (ledPin 21 HIGH));
        T --> U(Blynk.virtualWrite V3: Sedang Hujan);
        U --> V(Blynk.virtualWrite V4: LED ON/Hijau (255));
        V --> W[END];
        
        Q -- TIDAK (Cerah) --> X(Tulis Servo ke posisi Buka: 100);
        X --> Y(Serial Print: CERAH = Buka jemuran);
        Y --> Z(Matikan LED Fisik (ledPin 21 LOW));
        Z --> AA(Blynk.virtualWrite V3: Tidak Hujan);
        AA --> BB(Blynk.virtualWrite V4: LED OFF/Hitam (0));
        BB --> W;
    end
    
    %% cekSuhu Function
    subgraph cekSuhu()
        CC[START] --> DD(Baca Suhu (dht.readTemperature) & Kelembapan (dht.readHumidity));
        DD --> EE{Apakah Suhu atau Kelembapan NaN?};
        
        EE -- YA --> FF(Serial Print: Gagal membaca sensor!);
        FF --> GG[Return/END];

        EE -- TIDAK --> HH(Serial Print Nilai Suhu & Kelembapan);
        HH --> II(Blynk.virtualWrite V0: Suhu);
        II --> JJ(Blynk.virtualWrite V1: Kelembapan);
        JJ --> KK(Delay 1000ms);
        KK --> GG;
    end
    
    L --> M;
    L --> N;
