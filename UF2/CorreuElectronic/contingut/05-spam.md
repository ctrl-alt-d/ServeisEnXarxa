## 5. El Problema de l'Spam

### 5.1. Què és l'spam?
L'spam fa referència als correus electrònics no desitjats o no sol·licitats que sovint es reben en grans volums. Aquest tipus de correus solen tenir finalitats comercials, però també poden incloure intents de phishing, malware o altres activitats fraudulentes.

- **Característiques principals:**
  - Enviament massiu sense consentiment.
  - Contingut sovint enganyós o de baixa qualitat.
  - Pot incloure enllaços maliciosos o fitxers adjunts perillosos.

- **Impactes negatius:**
  - Saturació de les bústies d'entrada.
  - Pèrdua de productivitat per als usuaris.
  - Riscos de seguretat, com infeccions de malware o robatori d'informació.

---

### 5.2. Estratègies per combatre l'spam

#### 5.2.1. Llistes negres i blanques

- **Llistes Negres:**
  - Contenen adreces IP o dominis coneguts per enviar spam.
  - Els correus provinents d'aquests orígens es bloquegen automàticament.
  - Exemples de serveis de llistes negres: Spamhaus, Barracuda, etc.

- **Llistes Blanques:**
  - Contenen adreces IP o dominis de confiança que sempre són acceptats.
  - Útil per garantir la recepció de correus importants.

- **Avantatges:**
  - Fàcils d'implementar.
  - Redueixen significativament l'entrada d'spam conegut.

- **Desavantatges:**
  - Dependència d'actualitzacions constants per ser efectives.
  - Potencial de falsos positius o negatius.

#### 5.2.2. Sistemes d'anàlisi del contingut

- **Anàlisi Heurístic:**
  - Examina les característiques dels correus, com paraules clau sospitoses, enllaços i estructures.
  - Assigna una puntuació de risc i marca els correus amb puntuacions altes com a spam.

- **Filtres Bayesians:**
  - Utilitzen tècniques d'aprenentatge automàtic per identificar patrons comuns en els correus electrònics d'spam.
  - Els filtres milloren amb el temps gràcies a l'entrenament amb exemples d'spam i correus legítims.

- **Anàlisi de reputació:**
  - Considera la reputació de l'adreça IP o domini de l'enviador.

- **Avantatges:**
  - Molt efectius per detectar correus desconeguts o de noves campanyes d'spam.
  - S'adapten als canvis en les tàctiques dels spammers.

- **Desavantatges:**
  - Requereixen potència de processament per analitzar grans volums de correu.
  - Possibilitat d'errors en la classificació (falsos positius).

---

Amb aquestes estratègies, es pot reduir significativament l'impacte de l'spam, millorant tant la seguretat com la productivitat dels usuaris.
