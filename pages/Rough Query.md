- {{query (and [[ent-prof-card]] [[External Ear]] )}}
  query-table:: false
  collapsed:: true
-
- collapsed:: true
  #+BEGIN_QUERY
  {:title "All pages have a *programming* tag"
   :query [:find ?name
         :in $ ?tag1 ?tag2
         :where
         [?t1 :block/name ?tag1]
         [?t2 :block/name ?tag2]
         [?p :block/tags ?t1]
         [?p :block/tags ?t2]
         [?p :block/name ?name]]
   :inputs ["disease" "neoplasia"]
   :view (fn [result]
         [:div.flex.flex-col
          (for [page result]
            [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
-
- {{query (and [[ent-prof-card]] "Clinical")}}
  collapsed:: true
- {{query (and #ent-prof-card #definition ) }}
  collapsed:: true
- {{query (and #definition (or #ortho-prof-card #ortho-prof-written ) ) }}
  collapsed:: true
- {{query (and [[ortho-prof-card]] [[ortho-prof-written]] )}}
  collapsed:: true
- {{query (and "Classification" (or #ortho-prof-card #ortho-prof-written ) ) }}
  collapsed:: true
-
-
- fdafasssss
  id:: 6776bb0d-6876-4da1-915f-66cc8f7bd4ca
- ```
  query.pages()
        .property('tags')
            .reference('book')
        .noProperty('category')
        .getNames()
  ```
- length of ent
	- {{renderer :view, "query.pages().tags('ent').getNames().map((p) => p.title).join('\n')"}}
- {{{
  var books = query.pages()
    .tags('book')
    .get()
  }}}
- {{renderer :view, query.table(books)}}
- {{renderer :view, c.page.name}}
- ```javascript
  ``{
    var books = query.pages().tags('book').get()
  }``
  ```
- sda
  {{renderer :view, "query.table(query.pages().tags(['surgery','ent']).get(),['page','knowledge'])"}}
-