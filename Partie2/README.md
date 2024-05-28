# TP NoSQL - Partie 2: Premiers pas avec le cluster Elasticsearch

## Objectifs
- Comprendre les opérations de base
- Appréhender le mapping de données

## Étapes réalisées

### Étape 1: Créer un index nommé `test01`

J'ai utilisé la commande `Invoke-WebRequest`, équivalente à `curl` en PowerShell, pour créer un index nommé test01 :

`Invoke-WebRequest -Method Put -Uri "http://localhost:9200/test01"`

### Étape 2: Vérifier que cet index est présent
Pour vérifier que l'index est bien présent, j'ai utilisé la commande suivante :
`Invoke-WebRequest -Uri "http://localhost:9200/_cat/indices?v"`

Voici la réponse obtenue :
```
PS D:\Dropbox\Enzo\Dev\NoSQL\Elasticsearch & Kibana> Invoke-WebRequest -Method Put -Uri "http://localhost:9200/test01"


StatusCode        : 200
StatusDescription : OK
Content           : {"acknowledged":true,"shards_acknowledged":true,"index":"test01"}
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 66
                    Content-Type: application/json; charset=UTF-8

                    {"acknowledged":true,"shards_acknowledged":true,"index":"test01"}
Forms             : {}
Headers           : {[Content-Length, 66], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 66
```

### Étape 3: Créer un document dans cet index

J'ai créé un fichier `document.json` contenant le corps du document, puis exécuté la commande suivante pour créer un document dans l'index test01 :

```
{
    "titre": "Premier document",
    "description": "C'est la première fois que je crée un document dans ES.",
    "quantité": 12,
    "date_creation": "10-05-2024"
}
```
Voici la commande que j'ai ensuite exécuté pour créer un document dans cette index : 
```
Invoke-WebRequest -Method Post -Uri "http://localhost:9200/test01/_doc/1" `
>>   -ContentType "application/json" `
>>   -Body $body
```

Réponse du terminal : 

```
StatusCode        : 201
StatusDescription : Created
Content           : {"_index":"test01","_type":"_doc","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":2,"failed":0},"_seq_no":0,"_primary_term":1}
RawContent        : HTTP/1.1 201 Created
                    Content-Length: 154
                    Content-Type: application/json; charset=UTF-8
                    Location: /test01/_doc/1

                    {"_index":"test01","_type":"_doc","_id":"1","_version":1,"result":"created","_shar...
Forms             : {}
Headers           : {[Content-Length, 154], [Content-Type, application/json; charset=UTF-8], [Location, /test01/_doc/1]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass                                                                                                                                                                          RawContentLength  : 154
```

### Étape 4: Afficher ce document

Pour afficher le document créé, j'ai utilisé la commande suivante : 
`Invoke-WebRequest -Uri "http://localhost:9200/test01/_doc/1"`

Réponse du terminal :

```
StatusCode        : 200
StatusDescription : OK
Content           : {"_index":"test01","_type":"_doc","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":{
                        "titre": "Premier document",
                        "description": "C'est la première fois que je cré...
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 293
                    Content-Type: application/json; charset=UTF-8

                    {"_index":"test01","_type":"_doc","_id":"1","_version":1,"_seq_no":0,"_primary_term":1,"found":true,"_source":{
                    ...
Forms             : {}
Headers           : {[Content-Length, 293], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 293
```

### Étape 5: Afficher le mapping appliqué par défaut

J'ai utilisé la commande suivante pour afficher le mapping par défaut :
`Invoke-WebRequest -Uri "http://localhost:9200/test01/_mapping?pretty"`

Réponse du terminal : 
```
StatusCode        : 200
StatusDescription : OK
Content           : {
                      "test01" : {
                        "mappings" : {
                          "properties" : {
                            "date_creation" : {
                              "type" : "text",
                              "fields" : {
                                "keyword" : {
                                  "type" : "keyword",
                       ...
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 759                                                                                                                                                                                                   Content-Type: application/json; charset=UTF-8                                                                                                                                                                                                                                                                                                                                                                                               {                                                                                                                                                                                                 
                      "test01" : {
                        "mappings" : {
                          "properties" : {
                            "date_creation" : {
                              "type" : "text",...
Forms             : {}
Headers           : {[Content-Length, 759], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 759
```

### Étape 6: Modifier le mapping pour le champ `date_creation`
La commande utilisée pour modifier le mapping du champ date_creation était la suivante :
` Invoke-WebRequest -Method Put -Uri "http://localhost:9200/test02" `                                                                                       
```
>>   -ContentType "application/json"                                
>>   -Body '{
>>     "mappings": {
>>       "properties": {
>>         "date_creation": {
>>           "type": "date",
>>           "format": "dd-MM-yyyy"
>>         }
>>       }
>>     }
>>   }'
```

Réponse du terminal : 
```
StatusCode        : 200
StatusDescription : OK
Content           : {"acknowledged":true,"shards_acknowledged":true,"index":"test02"}
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 65
                    Content-Type: application/json; charset=UTF-8

                    {"acknowledged":true,"shards_acknowledged":true,"index":"test02"}
Forms             : {}
Headers           : {[Content-Length, 65], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 65
```

### Étape 7: Afficher la manière dont le texte de la description du document a été traité par l'Analyzer

La commande utilisée pour analyser le texte de la description était la suivante :
```
Invoke-WebRequest -Method Post -Uri "http://localhost:9200/test02/_analyze" -ContentType "application/json" -Body '{
  "field": "description",
  "text": "C'est la première fois que je crée un document dans ES."
}'
```

Réponse du terminal : 

```
StatusCode        : 200
StatusDescription : OK
Content           : {"tokens":[{"token":"C'est la première fois que je crée un document dans ES.","start_offset":0,"end_offset":4,"type":"<ALPHANUM>","position":0}]}
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 94
                    Content-Type: application/json; charset=UTF-8

                    {"tokens":[{"token":"C'est la première fois que je crée un document dans ES.","start_offset":0,"end_offset":4,"type":"<ALPHANUM>","position":0}]}
Forms             : {}
Headers           : {[Content-Length, 94], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 94
```

### Étape 8: Créer un index test03 avec un mapping personnalisé

La commande utilisée pour créer l'index test03 avec un mapping personnalisé était la suivante :
```
Invoke-WebRequest -Method Put -Uri "http://localhost:9200/test03" -ContentType "application/json" -Body '{
  "settings": {
    "analysis": {
      "analyzer": {
        "custom_analyzer": {
          "type": "standard",
          "stopwords": "_french_"
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "description": {
        "type": "text",
        "fields": {
          "raw": {
            "type": "keyword"
          },
          "analyzed": {
            "type": "text",
            "analyzer": "custom_analyzer"
          }
        }
      },
      "date_creation": {
        "type": "date",
        "format": "dd-MM-yyyy"
      }
    }
  }
}'
```

Réponse du terminal :

```
StatusCode        : 200
StatusDescription : OK
Content           : {"acknowledged":true,"shards_acknowledged":true,"index":"test03"}
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 65
                    Content-Type: application/json; charset=UTF-8

                    {"acknowledged":true,"shards_acknowledged":true,"index":"test03"}
Forms             : {}
Headers           : {[Content-Length, 65], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 65
```

### Étape 9: Faire un import multiple de 4 documents en une seule requête

Pour éviter les problèmes de parsing, je recommande de créer un fichier contenant les données de la requête _bulk et de le lire dans PowerShell. Voici comment j'ai procédé :
1. J'ai créé un fichier JSON contenant les données pour la requête _bulk et j'ai utilisé PowerShell pour envoyer le contenu du fichier à Elasticsearch en une seule requête :
```
{ "index": { "_index": "test03", "_id": "1" } }
{ "titre": "Premier document", "description": "C'est la première fois que je crée un document dans ES.", "quantité": 12, "date_creation": "10-05-2024" }
{ "index": { "_index": "test03", "_id": "2" } }
{ "titre": "Deuxième document", "description": "C'est le deuxième document.", "quantité": 5, "date_creation": "11-05-2024" }
{ "index": { "_index": "test03", "_id": "3" } }
{ "titre": "Troisième document", "description": "Voici un troisième document.", "quantité": 7, "date_creation": "12-05-2024" }
{ "index": { "_index": "test03", "_id": "4" } }
{ "titre": "Quatrième document", "description": "Ceci est le quatrième document.", "quantité": 10, "date_creation": "13-05-2024" }
```

2. Charger le contenu du fichier JSON dans une variable : 
`$bulkData = Get-Content -Path "D:\Dropbox\Enzo\Dev\NoSQL\Elasticsearch & Kibana\Partie 2\bulk_data.json" -Raw`

3. Utiliser PowerShell pour lire et envoyer le contenu du fichier : 
```
$bulkData = Get-Content -Path "D:\Dropbox\Enzo\Dev\NoSQL\Elasticsearch & Kibana\Partie 2\bulk_data.json" -Raw
Invoke-WebRequest -Method Post -Uri "http://localhost:9200/test03/_bulk" `
  -ContentType "application/json" `
  -Body $bulkData
```

Réponse du terminal : 
```
StatusCode        : 200
StatusDescription : OK
Content           : {"took":186,"errors":false,"items":[{"index":{"_index":"test03","_type":"_doc","_id":"1","_version":1,"result":"created","_shards":{"total":2,"successful":2,"failed":0},"_seq_no":0,"_primary_te
                    rm":1,"...
RawContent        : HTTP/1.1 200 OK
                    Content-Length: 749
                    Content-Type: application/json; charset=UTF-8

                    {"took":186,"errors":false,"items":[{"index":{"_index":"test03","_type":"_doc","_id":"1","_version":1,"result":"c...
Forms             : {}
Headers           : {[Content-Length, 749], [Content-Type, application/json; charset=UTF-8]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 749
``` 

**Explication :**
- **$bulkData :** Cette variable lit le contenu du fichier bulk_data.json en une seule chaîne brute (-Raw), ce qui évite les problèmes de parsing liés aux caractères spéciaux et à la structure de la requête _bulk.
- **Invoke-WebRequest :** Cette commande envoie la requête à Elasticsearch en utilisant le contenu du fichier comme corps de la requête.

## Conclusion : 
Cette partie du TP m'a permis d'acquérir les bases de l'utilisation d'Elasticsearch, depuis la création d'index jusqu'à l'insertion et à l'optimisation des documents. J'ai également compris les concepts de mapping et d'analyse textuelle, qui sont essentiels pour une utilisation efficace d'Elasticsearch dans des projets de gestion et de recherche de données.