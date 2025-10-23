# Sakha NER Annotation Guidelines

This document presents a flat BIO tagging scheme with three entity labels designed specifically for Sakha language processing. We annotate only proper names using non-overlapping spans without nesting. These guidelines were developed to ensure high inter-annotator agreement while addressing culturally specific Sakha naming conventions.

## Label Set

### PER (Person)

Covers personal names in their entirety, including given names, surnames, patronymics, established pseudonyms, and initials when they refer to specific individuals. This category encompasses both contemporary and historical figures, as well as mythological or literary characters when referenced as named entities.

**Examples:** *Айсен Сергеевич Николаев*, *Ньургун Боотур*, *Дьулуур*, *А.С. Пушкин*, *Суорун Омоллоон*

### ORG (Organization)

Covers formal organizations and institutions with established continuity, including governmental bodies (parliaments, ministries, committees), educational institutions (schools, universities, research institutes), cultural organizations (theaters, museums, libraries), media outlets, commercial entities (companies, banks), and standing councils or assemblies. The key criterion is institutional permanence rather than temporary formations.

**Examples:** *Ил Түмэн*, *СВФУ*, *Театр Олонхо*, *СахаМедиа*, *Саха АССР Үрдүкү Сэбиэтин*

### LOC (Location)

Covers geopolitical entities and geographic features, including administrative divisions (republics, uluses, districts), settlements (cities, towns, villages), geographic features (rivers, lakes, mountains, seas), and infrastructure (streets, squares, airports). Type words (e.g., *өрүс*, *куорат*, *улууһа*) are included when they appear together with the proper name in the same phrase; when the proper name appears independently, only the name itself is labeled.

**Examples:** *Дьокуускай куорат*, *Сунтаар улууһа*, *Өлүөнэ өрүс*, *Алдан*, *Республика Саха (Якутия)*

## Exclusions

The following categories are systematically excluded from annotation:

- Temporal expressions: *ыам ыйын биэстэ*, *2024 сыл*
- Professional titles or occupations: *суруйааччы*, *министр*, *дьокутаат*
- Adjectival forms and demonyms: *сахалыы*, *якутскай*, *российскай*
- Common nouns used generically: *оскуола*, *театр*, *өрүс* (when not part of a proper name)
- Ethnonyms and language names used descriptively: *саха*, *Саха тыл* (when referring to the language itself rather than a political entity)
- Legal entity designations standing alone: *ООО*, *АО*, *ГУП*, *ИП*

## Span Boundaries

Annotation spans include the complete proper name while systematically excluding titles, positions, and generic descriptors.

**Examples:**
- *президент Айсен Николаев* → annotate only *Айсен Николаев* as PER
- *Дьокуускайга* → annotate fully as LOC (case suffix *-га* preserved)
- *Сахаайата* → annotate fully as LOC (possessive marker preserved)
- *ООО «СахаМедиа»* → annotate only *СахаМедиа* as ORG (quotation marks excluded)
- *А.С. Пушкин* → annotate fully as PER (initials + surname unified)

Orthographic variations, including capitalization differences and spelling variants, do not affect entity boundaries; annotation follows semantic content while maintaining morphological suffixes:
- *ил түмэн*, *ИЛ Түмэн*, *Ил Түмэнин* → all labeled as complete ORG spans

## Non-Overlapping Annotation Policy

Given the flat structure of our scheme, entity spans cannot overlap.

### Maximal Span Rule

When a longer phrase constitutes a valid entity type, annotate only the maximal span without marking internal components.

**Example:** *Саха Өрөспүүбүлүкэтин Ил Түмэнин* (Parliament of the Sakha Republic) → annotate as single ORG span (do not separately mark the internal geographic reference)

### Internal Component Rule

When a longer phrase falls outside our target entity types but contains a valid PER, ORG, or LOC component, the valid internal span should be annotated.

**Examples:**
- *Саха Өрөспүүбүлүкэтин Конституцията* (Constitution of the Sakha Republic) → annotate *Саха Өрөспүүбүлүкэтин* as LOC ("constitution" is not an entity type)

Physical buildings or facilities without unique institutional names do not qualify as ORG entities.

## Events versus Organizations

A critical distinction exists between temporary events and permanent institutions.

**Excluded from ORG:** One-time events, conference sessions, specific forum editions, or temporal gatherings (regardless of their formal character)

**Included as ORG:** Entities with institutional continuity and ongoing operational functions

**Example:** *Советтар Бүтүн Саха сиринээҕи* → ORG only when denoting the permanent representative assembly, NOT when referring to a specific convening session (*Бүтүн Саха сиринээҕи съездэ*)

## Annotation Decision Process

For systematic annotation, apply the following sequential checks:

1. **Entity Type Validation:** Does the span represent a proper name belonging to PER, ORG, or LOC categories? If no, exclude from annotation.

2. **Span Completeness:** Select the complete proper-name span including morphological suffixes while excluding titles, positions, and legal designations.

3. **Overlap Resolution:** Would this annotation overlap with another span? If yes, retain only the larger valid entity. If the larger phrase falls outside our entity labels, annotate the valid internal span.

4. **Institutional Permanence:** For potential ORG entities, verify whether the referent represents a standing institution rather than a temporary event or session. Sessions and specific editions are excluded; permanent bodies receive ORG annotation.

5. **Boundary Finalization:** Apply standard boundary rules—exclude surrounding quotation marks while preserving official parenthetical elements, maintain grammatical suffixes within spans, and unify abbreviations that form part of the name.