# Einfache Artikel-Suchergebnisse

Dieses Suchergebnis-Modul nimmt einen Suchbegriff mittels GET/POST-Parameter `search` entgegen und gibt gefundene Artikel aus. Es werden keine im Backend gesetzten `Search it`-Einstellungen überschrieben.

## Modulausgabe (ohne Erläuterungen)

```
<section class="search_it-modul">
    <p class="search_it-demotitle">[search_it] Suchergebnisse - Einfaches Beispielmodul</p>
    <?php
$server = rtrim(rex::getServer(),"/"); 
$request = rex_request('search', 'string', false); 

if($request) { 
    $search_it = new search_it(); 
    $result = $search_it->search($request); 
	# dump($result); // Zum Debuggen ausgeben.

    if($result['count']) { 
        echo '<h2 class="search_it-headline">{{ Suchergebnisse }}</h2>'; 

        echo '<ul class="search_it-results">';                           
        foreach($result['hits'] as $hit) { 

            # dump($hit); 
            if($hit['type'] == 'article') { 
                $article = rex_article::get($hit['fid']); 
                echo '<li class="search_it-result search_it-article">
                          <p class="search_it-title">
                              <a href="'.$server.rex_getUrl($hit['fid'], $hit['clang'], array('search_highlighter' => $request)).'" title="'.$article->getName().'">'.$article->getName().'</a>
                          </p>
                          <p class="search_it-url">'.$server.rex_getUrl($hit['fid'], $hit['clang']).'</p>
                          <p class="search_it-teaser">'.$hit['highlightedtext'].'</p>
                      </li>'; 
            } else {                                   
                
                
                echo '<p class="search_it-missing_type">Das Suchergebnis vom Typ <i class="search_it-type">'.$hit['type'].' </i> kann nicht dargestellt werden.</p>';
            }

        } 
        echo '</ul>';
    } else if(!$result['count']) { 
        echo '<p class="search_it-zero">Die Suche nach <i class="search_it-request">'.rex_escape($request).' </i> ergab keine Treffer.</p>';
    }
} 
    ?>
</section>
```

## Modulausgabe (mit Erläuterungen)

```
<section class="search_it-modul">
    <p class="search_it-demotitle">[search_it] Suchergebnisse - Einfaches Beispielmodul</p>
    <?php
$server = rtrim(rex::getServer(),"/"); // Aktuelle Website-Adresse ohne Slash am Ende;
$request = rex_request('search', 'string', false); // GET/POST-Anfrage: Casting als String

if($request) { // Wenn ein Suchbegriff eingegeben wurde
    $search_it = new search_it(); // Suche initialisieren
    $result = $search_it->search($request); // Suche ausführen
	# dump($result); // Zum Debuggen ausgeben.

    if($result['count']) { // Wenn Ergebnisse vorhanden sind...
        echo '<h2 class="search_it-headline">{{ Suchergebnisse }}</h2>'; // Sprog-AddOn zur Übersetzung benutzen

        echo '<ul class="search_it-results">';                           
        foreach($result['hits'] as $hit) { // Jeder Treffer in $hit

            # dump($hit); // Zum Debuggen ausgeben.
            if($hit['type'] == 'article') { // Wenn der Treffer aus der Artikelsuche stammt 
                $article = rex_article::get($hit['fid']); // REDAXO-Artikel-Objekt holen
                echo '<li class="search_it-result search_it-article">
                          <p class="search_it-title">
                              <a href="'.$server.rex_getUrl($hit['fid'], $hit['clang'], array('search_highlighter' => $request)).'" title="'.$article->getName().'">'.$article->getName().'</a>
                          </p>
                          <p class="search_it-url">'.$server.rex_getUrl($hit['fid'], $hit['clang']).'</p>
                          <p class="search_it-teaser">'.$hit['highlightedtext'].'</p>
                      </li>'; // Ausgabe des Suchtreffers
            } else {                                   
                // Wenn der Treffer nicht aus REDAXO-Artikeln stammt, z.B., weil Medienpool oder Datenbankspalten
                // indiziert wurden. Siehe erweiterte Beispiele für die Ausgabe. Oder: Indexierung auf Artikel beschränken.
                echo '<p class="search_it-missing_type">Das Suchergebnis vom Typ <i class="search_it-type">'.$hit['type'].' </i> kann nicht dargestellt werden.</p>';
            }

        } // foreach($result['hits'] as $hit) END
        echo '</ul>';
    } else if(!$result['count']) { // Wenn keine Ergebnisse vorhanden sind.... 
        echo '<p class="search_it-zero">Die Suche nach <i class="search_it-request">'.rex_escape($request).' </i> ergab keine Treffer.</p>';
    }
} // if($request) END
    ?>
</section>
```

## CSS

Das Sucheingabe-Formular kann beliebig formatiert und mit Klassen ausgezeichnet werden. Das nachfolgende CSS formatiert das oben vorgegebene Beispiel.

```
<style>
    /* Diese CSS-Datei in das Design ausschneiden und anpassen */
    .search_it-modul {
        box-sizing: border-box;
        font-size: 1rem;
        font-family: sans-serif;
        max-width: 640px;
        margin: 0 auto;
        border: 1px solid rgba(0,0,0,0.2);
        padding: 0 2rem 2rem 2rem;
    }
    .search_it-demotitle {
        font-size: 1.2rem;
        font-weight: bold;
        border-bottom: 1px solid  rgba(0,0,0,0.2);
        color: rgba(0,0,0,0.4);
        margin-bottom: 2rem;
    }
    
    .search_it-results {
        padding: 0;
        margin: 0;
    }
    .search_it-result {
        background: rgba(0,0,0,0.05);
        border: 1px solid rgba(0,0,0,0.4);
        padding: 1rem;
        margin: 1rem 0;
        list-style-type: none;
        list-style-position: inline;
    }
    .search_it-title,
    .search_it-title a {
        font-weight: bold;
        color: rgba(0,180,0,0.7);
    }

    .search_it-result .search_it-teaser {
        color: rgba(0,0,0,0.7);
    }
    .search_it-result .search_it-url {
        color: rgba(0,0,0,0.4);
    }
    
    .search_it-missing_type,
    .search_it-zero {
        background: rgba(180,0,0,0.05);
        padding: 1rem;
        border: 1px solid rgba(255,0,0,0.7);
        margin: 1rem 0;
        color: rgba(255,0,0,0.7);
    }
    .search_it-request,
    .search_it-type {
        font-weight: bold;
    }
</style>
```
