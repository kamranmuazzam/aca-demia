- Prev Cla
  collapsed:: true
	- ## Diseases of Ear
		- ### [[Diseases of External Ear]]
		- ### Diseases of [[Middle Ear]]
			- [[Acute Suppurative Otitis Media]]
			- [[Chronic Suppurative Otitis Media]]
			- [[Glue Ear]]
		- ### Diseases [[Internal Ear]]
	- ## Diseases of Nose
	  collapsed:: true
		- [[Epistaxis]]
		- [[Deviated Nasal Septum]]
		- [[Sinusitis]]
		- [[Nasal Polyp]]
		- Difference between [[Otogonal polyp & Athmoidal Polyp]]
	- ## Oropharynx
	  collapsed:: true
		- [[Tonsillectomy]]
	- ## Throat
	  collapsed:: true
		- [[Tracheostomy]]
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
  :inputs [:current-page "congenital disease"]
  :view (fn [result]
       [:div.flex.flex-col
        (for [page result]
          [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
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
   :inputs [:current-page "inflammatory disease"]
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
  :inputs [:current-page "neoplasia"]
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
  :inputs [:current-page "degenerative disease"]
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
  :inputs [current-page "trauma induced disease"]
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
  :inputs [:current-page "surgical procedure"]
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
  :inputs ["ent" "appliance"]
  :view (fn [result]
   [:div.flex.flex-col
    (for [page result]
      [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)])])}
  #+END_QUERY
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-