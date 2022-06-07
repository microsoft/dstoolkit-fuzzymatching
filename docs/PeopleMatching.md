# People Matching

## Concepts

### Skill Ontologies

Generally in people matching scenarios, people are matched job, project, or learning opportunities by matching a set of skills possessed by the person to the set of skills from the job posting, project request, or learning description. These skills can come from structured (i.e. skills database) or unstructured sources (i.e. resumes) and come from an a known set of skills (an ontology), or can be derived via a data-driven ontology. These approaches can generally be broken down into the following:

- **Traditional Knowledge Management:** Manually maintained skill ontologies (i.e. an employee skills database), contains only structured skills ratings, generally from users rating their own skills. This approach is often the most cumbersome to maintain, becomes quickly outdated, and is often subject to bias from user ratings.
- **Application Tracking Systems (ATS):** Commonly used tool for recruiting, extracts skills from unstructured candidate resumes and matches against skills extracted from job descriptions. Generally uses a skill ontology maintained by the recruiting/staffing company.
- **Personalized Expertise Finders:** The most advanced systems which derive their ontology from the structured and unstructured data sources themselves using fuzzy matching and various NLP approaches. These systems are able to bring together multiple sources to get a fuller picture of the candidate and provide more personalized results.

![Traditional Knowledge Mining vs. Expertise Finder Systems](/docs/fuzzy_matching_people_matching_skills_ontologies.png)

This diagram explains the general benefits of expertise finder systems compared to traditional knowledge management systems.

### Skill Ontology Sources

If you are building a People Matching system that requires an existing skill ontology there are a few existing sources that can be leveraged:

- **Customer's skill ontology:** if the customer is a recruiting or staffing agency, they will likely have an existing skill ontology. The benefit of using their existing ontology is that it will likely be more catered to the types of skills they recruit but the disadvantage is that it may not be as encompassing as other skill ontologies.
- **Azure Cognitive Services Named Entity Recognition (NER) Skill Entity:** [Azure Cognitive Services NER](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/named-entity-types?tabs=general) can extract specific entities from structured and unstructured data sources using a pre-built NER model. In version 3.0 of NER, a skills entity was added, the result of work done by the Worldwide Learning Innovation Team. This taxonomy was built using a record linkage model built on open sources including Coursera, Microsoft Academic Graph, Github Featured Topics, Stackshare Tools, and more. Examples of a job matching demo and product built by the WWL Innovation team using this taxonomy can be found here [Using Azure Search custom skills to create personalized job recommendations](https://azure.microsoft.com/en-us/blog/using-azure-search-custom-skills-to-create-personalized-job-recommendations/) and Project Lagro (find introduction deck in the *docs* folder).
- **LinkedIn Enterprise Standardized Skills Ontology** [LinkedIn](https://developer.linkedin.com/docs/ref/v2/standardized-data/skills) maintains a set of ~35K standardized skills based on skills used by LinkedIn's 350 million member base. These skills are used for LinkedIn's skill recommendations, job matching, and recruiter tools. Access to [LinkedIn's Standardized Data Skill API](https://developer.linkedin.com/docs/ref/v2/standardized-data/skills) requires an enterprise license and strict legal contract between your customer and LinkedIn as this is regarded as LinkedIn IP and can only be used under limited terms.

### Derived Ontology Sources

For building People Matching scenarios using derived skill ontologies, it is important to first define what skill expertise means and then to identify possible data sources for the different components of skill expertise. Based on research on the design of expertise finding systems, skill expertise can be generally divided into the 4 areas shown below:

![Derived Ontology Sources](/docs/fuzzy_matching_people_matching_ontology_sources.png)

In addition, a list of some of the common data sources used (both structured and unstructured) that represent explicit or implicit indicators of skill expertise can be found below:

![Implicit Indicators vs. Explicit Indicators](/docs/fuzzy_matching_people_matching_indicators.png)

Additional details on approaches and a demo for automated expertise finding can be found in the *docs* folder.

---

## Architecture

### First-Party Azure Architecture

![First-Party Azure Architecture](/docs/fuzzy_matching_people_matching_architecture.png)



