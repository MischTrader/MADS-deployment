# Nijmeegse Straattaal Generator

Dit project bevat een gedeployde NLP-applicatie die nieuwe Nijmeegse straattaalwoorden genereert op basis van een getraind RNN-model.  
De applicatie bestaat uit een FastAPI-backend, een eenvoudige HTML/CSS-frontend en is volledig gedockerized voor deployment op SURF.

## Live deployment
De applicatie draait op een SURF VM en is bereikbaar via: http://145.38.194.28/
(HTTP op poort 80)

## Projectoverzicht
- **Dataset**: Nijmeegs dialect van mijnwoordenboek.nl  
- **Model**: Character-level RNN (PyTorch)  
- **Backend**: FastAPI  
- **Frontend**: HTML / CSS / JavaScript  
- **Packaging**: Python wheel (`slanggen`)  
- **Deployment**: Docker + docker-compose  
- **Reproduceerbaarheid**: configuratie via `slanggen.toml`

## Mappenstructuur
straattaal/
├── backend/ # FastAPI applicatie
├── frontend/ # Statische frontend (HTML/CSS)
├── artefacts/ # Getraind model, tokenizer en config
├── dist/ # Gebouwde wheel (slanggen)
├── Dockerfile
├── docker-compose.yml
├── Makefile
├── slanggen.toml
└── README.md

## Modeltraining
De modeltraining wordt aangestuurd via `slanggen.toml`.  
De dataset wordt automatisch gedownload indien deze nog niet aanwezig is.

```bash
uv sync --all-extras
source .venv/bin/activate
python src/slanggen/main.py
```

## Na training bevat de map artefacts/:
- model.pth
- tokenizer.json
- config.json
- history.txt

## Het model en de preprocessing code zijn verpakt als een Python wheel.
uv build --clear
Dit genereert: dist/slanggen-0.4-py3-none-any.whl

## De Makefile automatiseert het volledige proces:
make
make run

## Wat make doet:
- controleert of de wheel bestaat (zo niet: build)
- controleert of het model bestaat (zo niet: train)
- bouwt de Docker image
- De applicatie draait vervolgens op poort 80.

## De aanbevolen manier om de applicatie te starten is via de Makefile:
```bash
make
make run
``` 

## Onderliggend worden de volgende Docker-commando’s uitgevoerd:
docker build -t straattaal-backend .
docker run -p 80:80 straattaal-backend

Docker Compose (SURF deployment)
docker compose up -d

## De service is ingesteld met:
restart: unless-stopped
poortmapping 80:80

## API endpoints
/ – frontend
/generate?num_words=10&temperature=1.0 – genereer woorden
/starred – beheer favoriete woorden
/health – health check

## Reproduceerbaarheid
Alle hyperparameters en paden zijn vastgelegd in slanggen.toml.
Door deze configuratie te wijzigen kan het experiment eenvoudig worden herhaald met een andere dataset.

## Auteur: Mischa Peters
## Opleiding: MADS 2025-2026 