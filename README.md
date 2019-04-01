# Your First Sixty Minutes with SPARQL

[![A screensaver video featuring a sparkly discoball.](https://img.youtube.com/vi/zymRNmdzF2k/0.jpg)](https://www.youtube.com/watch?v=zymRNmdzF2k)
*Something sparkly.*

These are notes from a one-hour workshop on October 10, 2018 at the University of Chicago Library. 
 
[“SPARQL in 11 Minutes” is a good, quick introduction to SPARQL](https://www.youtube.com/watch?v=FvGndkpa4K0)

## wikidata.org

Wikidata provides a SPARQL endpoint with lots of interesting data to experiment with. 
 
1. Go to [https://query.wikidata.org](https://query.wikidata.org).
2. One of the simplest possible queries just retrieves ten triples in the system:
```sparql
SELECT *
WHERE {
  ?s ?p ?o .
}
LIMIT 10
```
 
3. See ten distinct predicates in the system:

```sparql
SELECT DISTINCT ?p
WHERE {
  ?s ?p ?o .
}
LIMIT 10
```
 
4. Find some humans:
```sparql 
SELECT ?human
WHERE {
  ?human wdt:P31 wd:Q5 .
}
LIMIT 10
```

5. Find Kevin Bacon (he’s wd:Q3454165):
```sparql
SELECT DISTINCT ?kevin
WHERE {
  ?kevin rdfs:label "Kevin Bacon"@en .
}
```
 
6. Get a list of movies Kevin Bacon was in:
```sparql
SELECT DISTINCT ?film ?filmLabel
WHERE {
  ?film wdt:P161 ?actor .
  ?actor rdfs:label "Kevin Bacon"@en .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en" .
  }
}
```
 
7. Get a list of actors from “A Few Good Men”:
```sparql 
SELECT DISTINCT ?actor ?actorLabel
WHERE {
  wd:Q108946 wdt:P161 ?actor .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en" .
  }
}
```
 
8. Get a list of first degree connections (about a thousand of them):
```sparql
SELECT DISTINCT ?film ?filmLabel ?costar ?costarLabel
WHERE {
  ?film wdt:P161 ?kevin .
  ?kevin rdfs:label "Kevin Bacon"@en .
  ?film wdt:P161 ?costar .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en" .
  }
}
```
 
9. A simpler way to say “bacon 1” using property paths:
```sparql 
SELECT DISTINCT ?film ?filmLabel ?costar ?costarLabel
WHERE {
  ?film wdt:P161/rdfs:label "Kevin Bacon"@en .
  ?film wdt:P161 ?costar .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en" .
  }
}
```
 
10. Another way to say the same thing, using the comma:
```sparql 
SELECT DISTINCT ?film ?filmLabel ?costar ?costarLabel
WHERE {
  ?kevin rdfs:label "Kevin Bacon"@en .
  ?film wdt:P161 ?kevin, ?costar .
  SERVICE wikibase:label {
    bd:serviceParam wikibase:language "[AUTO_LANGUAGE],en" .
  }
}
```

## MarkLogic

The library's MarkLogic server includes a SPARQL endpoint too.
 
1. Get some triples from the system:
```sparql 
SELECT DISTINCT ?p
WHERE {
  ?s ?p ?o . 
}
LIMIT 10
```
 
2. Get some information about instances:
```sparql
SELECT DISTINCT ?instance ?p ?o
WHERE {
  ?instance <http://www.w3.org/1999/02/22-rdf-syntax-ns#type> <http://id.loc.gov/ontologies/bibframe/Instance> .
  ?instance ?p ?o . 
}
LIMIT 100
```
 
3. Get some information about a given Share-VDE instance:
```sparql 
SELECT *
WHERE {
  <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO1000046> ?p ?o .
}
LIMIT 100
```

4. I want to learn something about the agents for a specific instance:
```sparql 
SELECT *
WHERE {
 <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO1000046> <http://id.loc.gov/ontologies/bibframe/adminMetadata>/<http://id.loc.gov/ontologies/bibframe/agent>/<http://www.w3.org/2000/01/rdf-schema#label> ?agentLabel .
}
LIMIT 100
```
 
5. I want to see some agents:
```sparql 
SELECT *
WHERE {
 <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO1000046> <http://id.loc.gov/ontologies/bibframe/adminMetadata>/<http://id.loc.gov/ontologies/bibframe/agent> ?agent .
 ?agent <http://www.w3.org/2000/01/rdf-schema#label> ?agentLabel .
}
LIMIT 100
```
 
6. I want to learn something about extents. If I take the extent URI for this
and move it from subject to object in a triple, I can see what instances share
an extent. I see that instances that don’t have anything to do with each other,
other than the fact that they share a height and page count. 
```sparql 
SELECT *
WHERE {
<http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO1000046> <http://id.loc.gov/ontologies/bibframe/extent> ?extent .
}
LIMIT 10
```
 
7. You can do the same thing with notes. Then, take one of the note URIs and
look for triples where that note appears in the subject or object of triples in
the system:
```sparql 
SELECT *
WHERE {
 ?note ?p <http://id.loc.gov/ontologies/bibframe/Note> .
 ?note <http://www.w3.org/2000/01/rdf-schema#label> ?label .
}
LIMIT 100
```
 
8. Get works for a set of instances:
```sparql 
SELECT DISTINCT ?work
   WHERE {
     ?instance <http://id.loc.gov/ontologies/bibframe/instanceOf> ?work .
     FILTER (?instance IN (<http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO10026688>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO10074986>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO11281511>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO11375742>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO1197100>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO1689165>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO202479>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO2185933>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO2454733>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO337867>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO3762282>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO3762286>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO3762297>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4026318>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4070527>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4117779>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4234222>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4819830>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4824937>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4853548>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4885037>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO4906222>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO5820871>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO5821459>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO6104224>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO7467333>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO7898288>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO8389179>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO887407>,
                           <http://share-vde.org/sharevde/rdfBibframe2/Instance/UCHICAGO9042135>))
   }
```
 
8. Then, get the instances for one or more works:
```sparql 
SELECT DISTINCT ?instance
   WHERE {
     ?instance <http://id.loc.gov/ontologies/bibframe/instanceOf> ?work .
     FILTER (?work IN (<http://share-vde.org/sharevde/rdfBibframe2/Work/23955921>))
   }
```
 
## More Resources
 
[Learning SPARQL](http://shop.oreilly.com/product/0636920020547.do)
[Share-VDE User Guide](http://share-vde.org/sharevde/files/Guida_Utente_en.pdf)
