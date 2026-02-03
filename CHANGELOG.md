# Changelog

All notable changes to the CE-RISE Traceability and Life Cycle Events Data Model will be documented in this file.

## [0.0.3] - 2026-02-03

### Breaking Changes
- **BREAKING**: Renamed classes and fields from "product" to "entity" terminology (`ProductHistory` → `EntityHistory`, `linked_product_identifier` → `linked_entity_identifier`, `input_products` → `input_entities`, etc.)
- **BREAKING**: Updated SQL identifiers to reflect entity terminology

### Added
- Explicit support for Digital Material Passports (DMP) alongside Digital Product Passports (DPP)
- Unified "entity" terminology supporting products, materials, batches, and commodities
- DMP-related keywords to citation metadata

### Changed
- All documentation now uses "entity" terminology with explicit coverage of products, materials, batches, and commodities

## [0.0.2] - 2025-12-16

### Added
- References from Beta release of the data model

## [0.0.1] - 2025-12-03

### Added
- Initial project structure and repository setup from template: https://ce-rise-models.codeberg.page/template-data-model/
- Complete data model structure for traceability and lifecycle events aligned with GS1 EPCIS 2.0
- Six core event categories: ProductHistory, OwnershipEvent, ValueAddingActivityLocation, ActorTracking, LogisticsEvents, ReverseLogistics
- Two additional event categories for comprehensive supply chain coverage: AggregationEvents and SupplyChainIncidents
- EPCIS 2.0 compatibility with proper vocabulary alignment using owl.filler annotations
- Support for GS1 standards (GLN, GTIN, EPC URIs) and UN/LOCODE for locations
- Comprehensive enumerations for ownership status, return reasons, recycling streams, disposal methods, packaging levels, incident types, severity levels, and resolution status
- SQL-friendly identifier system with trc_ prefix and hierarchical namespacing
- Integration with CE-RISE architecture through cross-model references
- Pattern validation for standard formats (GLN, UN/LOCODE, ISO durations, currency codes)
- Error declaration support for event corrections
- Sensor metadata support for IoT integration
- Complete reverse logistics pathway management including refurbishment, recycling, and disposal events
- Supply chain incident tracking with severity classification and corrective action management
- Packaging hierarchy tracking through aggregation/disaggregation events
- Artifacts built and deployed to pages
