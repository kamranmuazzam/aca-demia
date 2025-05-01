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
- Study stuff
	- Ear
		- NOW [[Ménière's Disease]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 19:41:13]
		  CLOCK: [2025-04-21 Mon 19:41:14]
		  :END:
		- NOW [[ASOM]]
		  :LOGBOOK:
		  CLOCK: [2025-04-22 Tue 13:12:49]
		  CLOCK: [2025-04-22 Tue 13:12:52]
		  :END:
	- Throat
		- NOW [[Adenoid Facies]]
		- NOW [[Enlarged Adenoid]]
	- Operative
		- NOW [[Myringotomy]]
		- NOW [[Myringoplasty]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 18:35:17]--[2025-04-21 Mon 18:35:18] =>  00:00:01
		  CLOCK: [2025-04-21 Mon 18:35:18]--[2025-04-21 Mon 18:35:19] =>  00:00:01
		  CLOCK: [2025-04-21 Mon 18:35:20]--[2025-04-21 Mon 18:35:20] =>  00:00:00
		  CLOCK: [2025-04-21 Mon 18:35:31]
		  :END:
		- NOW [[Cortical Mastoidectomy]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 18:35:06]--[2025-04-21 Mon 18:35:07] =>  00:00:01
		  CLOCK: [2025-04-21 Mon 18:35:08]--[2025-04-21 Mon 18:35:10] =>  00:00:02
		  CLOCK: [2025-04-21 Mon 18:35:10]--[2025-04-21 Mon 18:35:21] =>  00:00:11
		  CLOCK: [2025-04-21 Mon 18:35:22]--[2025-04-21 Mon 18:35:23] =>  00:00:01
		  CLOCK: [2025-04-21 Mon 18:35:31]
		  :END:
		- NOW [[Radical Mastoidectomy]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 18:35:12]--[2025-04-21 Mon 18:35:24] =>  00:00:12
		  CLOCK: [2025-04-21 Mon 18:35:42]
		  :END:
		- NOW [[Modified Radical Mastoidectomy]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 18:35:12]--[2025-04-21 Mon 18:35:13] =>  00:00:01
		  CLOCK: [2025-04-21 Mon 18:35:14]--[2025-04-21 Mon 18:35:15] =>  00:00:01
		  :END:
		- NOW [[Cochlear Implant]]
		- NOW [[Tonsillectomy]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 18:36:19]
		  CLOCK: [2025-04-21 Mon 18:36:22]
		  :END:
		- NOW [[Adenoidectomy]]
		  :LOGBOOK:
		  CLOCK: [2025-04-21 Mon 18:36:27]
		  CLOCK: [2025-04-21 Mon 18:36:32]
		  :END:
- ## Developmental Disease
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
  :inputs [:current-page "developmental disease"]
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
            [:a {:href (str "#/page/" page)} (clojure.string/capitalize page)]
  )])
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
-
-
-