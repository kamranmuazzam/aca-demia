- High Yeilds : {{renderer :view, "query.pages().tags([c.page.name,'medicine']).property('high-yeild').value('true').get().length"}}
  title:: Medicine
- # Disease
  collapsed:: true
	- {{renderer :view, "query.table(query.pages().tags([c.page.name, 'disease']).get(), ['page', 'knowledge', 'high-yeild'])"}}
- # Clinical Presentation
  collapsed:: true
	- {{renderer :view, "query.table(query.pages().tags([c.page.name, 'clinical presentation']).get(), ['page', 'knowledge', 'high-yeild'])"}}
- Fluid
- Toxicology
  collapsed:: true
	- [[Organophosphorus Poisoning]] #fahim
	- [[Snake Bite]] #fahim
- Psychiatry
  collapsed:: true
	- [[Delusion]] #fahim
	- [[Hallucinations]] #fahim
	- [[Illusion]] #fahim
	- [[Schizophrenia]] #fahim
- [[SLE]]
- [[Down Syndrome]]
- [[Typhoid Fever]]
- Clinical Presentations
  collapsed:: true
	- [[Edema]]
	- [[Clubbing]]
	- [[Chest Pain]] #fahim
	- [[Dyspnea]] #fahim
	- [[Headache]] #fahim
	- [[Hemoptysis]] #fahim
	- [[Jaundice]] #fahim
	- [[Anaemia]] #fahim
	- [[Ascites]] #fahim
- [[Infectious Disease]]
- Examination
  {{renderer :view, "query.table(query.pages().tags([c.page.name,'examination']).get(),['page','knowledge','highYeild'])"}}