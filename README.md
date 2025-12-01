# CE-RISE Data Model Template

This repository provides the **official template** for creating CE-RISE data models.  
It defines the standard structure, tooling, and workflow used across all model projects.

---

## Repository structure

```
/
├─ model/          # LinkML source files (.yaml): required
├─ mappings/       # SSSOM and JSON-LD mappings: optional
├─ generated/      # locally generated JSON Schema, SHACL, OWL: optional - CI/CD deploys on pages
├─ samples/        # example data instances: optional
├─ tests/          # validation tests: optional
└─ README.md       # required
```

---

## Data Model Structure
Put here description of data model structure.

### Key Design Principles



### Data Properties

Each class has a corresponding value property (e.g., `name_value`, `company_id_value`) that holds the actual data. All value properties are string type except where specified otherwise.

#### SQL Identifiers

Every data point in the model includes a `sql_identifier` annotation that serves as a unique, machine-friendly database identifier. These identifiers follow a structured namespace pattern to ensure uniqueness across the entire data model:

**Pattern**: `MODEL_[category]_[specific_name]`

**Features:**
- **Product Profile Prefix**: All identifiers start, for instance, with `pro_` to clearly identify them as belonging to the Product Profile data model
- **Hierarchical Namespacing**: Uses category prefixes (`gen_info_`, `mfr_info_`, `imp_info_`, `spec_info_`) to provide context and prevent naming conflicts
- **Database-Friendly**: Uses underscores and avoids special characters for SQL compatibility
- **Unique Across Model**: No duplicate identifiers, even when similar concepts appear in different parts of the hierarchy
- **Reasonable Length**: Abbreviated but meaningful names that balance clarity with practical database usage

**Examples:**
- `pro_gen_info_gtin14` - GTIN-14 identifier in General Product Information
- `pro_mfr_info_facility` - Manufacturing facility in Manufacturer Information  
- `pro_imp_info_eori` - EORI number in Import/Export Information
- `pro_spec_info_materials` - Material composition in Product Specifications

This identifier system enables seamless integration with databases and ensures clear data model composition when combining with other CE-RISE data models.


---

## Purpose

Each data model in the CE-RISE ecosystem follows the same structure and build process.  
This ensures consistent validation, translation, and release formats across models.

Use this repository as a **template** when starting a new model:

1. On Codeberg, click **“Use this template”**.  
2. Name your new repository (for example `core-product-identification`).  
3. Clone it locally:
   ```bash
   git clone https://codeberg.org/CE-RISE_models/core-product-identification
   cd core-product-identification
   ```

---

## Define your model

Edit `model/model.yaml` to define your data classes and attributes.

Example:

```yaml
id: https://ce-rise-models.codeberg.page/core-product-identification/
name: core_product_identification
imports:
  - linkml:types
classes:
  Product:
    attributes:
      product_id:
        range: string
        identifier: true
      name:
        range: string
        description: Human-readable product name
```

---

## Generate schemas

The schemas are generated and published by the CI/CD automation. To produce the validation artifacts locally:

```bash
make all
```

This generates:
- `generated/schema.json` — JSON Schema for API validation  
- `generated/shacl.ttl` — SHACL for RDF validation  
- `generated/model.owl` — OWL ontology export  

You must commit generated files before pushing.

---

## Continuous integration

The repository includes a Forgejo Actions workflow at  
`.forgejo/workflows/build.yml`.  

It automatically:
- installs LinkML,  
- runs `make all`,  
- verifies that generated artifacts are up to date.
- deploys results on pages.

NOTE: actions must be activated in project settings to take advantage of this functionality.

---

## Publishing

Release artifacts for each version (`schema.json`, `shacl.ttl`, `model.owl`)  
are served directly from this URL:
```
https://ce-rise-models.codeberg.page/<repo-name>/
```

---

## Versioning

Follow [Semantic Versioning](https://semver.org/):

```bash
git tag -a v1.0.0 -m "First release"
git push origin v1.0.0
```

Each tag corresponds to a published release of the model.

---

## Accessing Previous Releases

If you want to view the files published for version `v1.2.0`, open:

```
https://codeberg.org/CE-RISE-models/<repo-name>/src/tag/pages-v1.2.0/generated/
```

Files available in that directory typically include:

- schema.json
- shacl.ttl
- model.ttl
- index.html

---

## Derived projects

All other CE-RISE model repositories must:
- keep the same directory layout,
- reference the template’s build workflow,
- host release files under `https://ce-rise-models.codeberg.page/<repo-name>/`.


---
<a href="https://europa.eu" target="_blank" rel="noopener noreferrer">
  <img src="https://ce-rise.eu/wp-content/uploads/2023/01/EN-Funded-by-the-EU-PANTONE-e1663585234561-1-1.png" alt="EU emblem" width="200"/>
</a>

Funded by the European Union under Grant Agreement No. 101092281 — CE-RISE.  
Views and opinions expressed are those of the author(s) only and do not necessarily reflect those of the European Union or the granting authority (HADEA).  
Neither the European Union nor the granting authority can be held responsible for them.

© 2025 CE-RISE consortium.  
Licensed under [Creative Commons Attribution–NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).  
Attribution: CE-RISE project (Grant Agreement No. 101092281) and the individual authors/partners as indicated.

<a href="https://www.nilu.com" target="_blank" rel="noopener noreferrer">
  <img src="https://nilu.no/wp-content/uploads/2023/12/nilu-logo-seagreen-rgb-300px.png" alt="NILU logo" width="40"/>
</a>

Developed by NILU (Riccardo Boero — ribo@nilu.no) within the CE-RISE project.  



