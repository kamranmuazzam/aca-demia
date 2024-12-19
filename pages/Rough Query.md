- {{query (and [[ent-prof-card]] [[External Ear]] )}}
  query-table:: false
  collapsed:: true
-
- #+BEGIN_QUERY
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