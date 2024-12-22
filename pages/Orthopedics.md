-
- # Orthopedic Emergencies #ortho-prof-card
	- [[Dislocation]]
	- [[Compartment Syndrome]]
	- [[Acute  Osteomyelitis in Children]]
	- [[Corda Equina Syndrome]]
- [[Cystic Lesions of Bone]]
- ## Congenital Disease
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
  :inputs [:current-page  "developmental disease"]
  :view (fn [result]
     [:div.flex.flex-col
      (for [page result]
        [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
	- ## Common congenital anomalies of bone origin #ortho-prof-card #ortho-prof-written
	- ## Evaluation of congenital anomalies of bone origin #ortho-prof-written
- ## Inflammatory Disease
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
  :inputs [:current-page  "inflammatory disease"]
  :view (fn [result]
       [:div.flex.flex-col
        (for [page result]
          [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])
  }
  #+END_QUERY
- ## Neoplasia
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
  :inputs [:current-page  "neoplasia"]
  :view (fn [result]
     [:div.flex.flex-col
      (for [page result]
        [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
- ## Degenerative Disease
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
  :inputs [:current-page  "degenerative disease"]
  :view (fn [result]
     [:div.flex.flex-col
      (for [page result]
        [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
- ## Trauma Induced Disease
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
  :inputs [:current-page  "trauma induced disease"]
  :view (fn [result]
     [:div.flex.flex-col
      (for [page result]
        [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
- ## Surgical Procedure
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
  :inputs [:current-page  "surgical procedure"]
  :view (fn [result]
     [:div.flex.flex-col
      (for [page result]
        [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
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
- ## Examination
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
  :inputs [:current-page "examination"]
  :view (fn [result]
   [:div.flex.flex-col
    (for [page result]
      [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY