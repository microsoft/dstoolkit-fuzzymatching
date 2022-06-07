# People Matching

## Concepts

### Skill Ontologies
Generally in people matching scenarios, people are matched job, project, or learning opportunities by matching a set of skills possessed by the person to the set of skills from the job posting, project request, or learning description. These skills can come from structured (i.e. skills database) or unstructured sources (i.e. resumes) and come from an a known set of skills (an ontology), or can be derived via a data-driven ontology. These approaches can generally be broken down into the following:

- **Traditional Knowledge Management:** Manually maintained skill ontologies (i.e. an employee skills database), contains only structured skills ratings, generally from users rating their own skills. This approach is often the most cumbersome to maintain, becomes quickly outdated, and is often subject to bias from user ratings.
- **Application Tracking Systems (ATS):** Commonly used tool for recruiting, extracts skills from unstructured candidate resumes and matches against skills extracted from job descriptions. Generally uses a skill ontology maintained by the recruiting/staffing company.
- **Personalized Expertise Finders:** The most advanced systems which derive their ontology from the structured and unstructured data sources themselves using fuzzy matching and various NLP approaches. These systems are able to bring together multiple sources to get a fuller picture of the candidate and provide more personalized results.

![Traditional Knowledge Mining vs. Expertise Finder Systems](/docs/fuzzy_matching_people_matching_skills_ontologies.png)




