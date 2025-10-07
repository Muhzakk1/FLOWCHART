```mermaid
graph TD
    A[START] --> B{SETUP};
    %% Setup Section
    B --> C(Inisialisasi);
    C --> D(Koneksi);
    D --> E(Atur Timer);
    E --> F{LOOP};
    F --> G(Cek Hujan);
    F --> H(Cek Suhu);
    G --> F;
    H --> F;
