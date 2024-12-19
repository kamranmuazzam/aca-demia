- [[Refractive Errors]]
- [[Cataract]]
- [[Chronic Dacryocystitis]]
- [[Terigium]]
- [[Kalazion]]
- [[Glaucoma]]
- ## Appliance
  #+BEGIN_QUERY
  {
  :query [:find ?name
   :in $ ?tag1 ?tag2
   :where
   [?t1 :block/name ?tag1]
   [?t2 :block/name ?tag2]
   [?p :block/tags ?t1]
   [?p :block/tags ?t2]
   [?p :block/name ?name]]
  :inputs [:current-page "appliance"]
  :view (fn [result]
   [:div.flex.flex-col
    (for [page result]
      [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY