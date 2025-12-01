# CE-RISE Data Model Template

This repository provides the **official template** for creating CE-RISE data models.  
It defines the standard structure, tooling, and workflow used across all model projects.


---

## Data Model Structure

The Traceability and Life Cycle Events data model captures **dynamic event-based information** throughout a product's journey in the supply chain and its entire lifecycle. This model is designed to be **compatible with GS1 EPCIS 2.0** (Electronic Product Code Information Services), the global standard for supply chain visibility, while extending it with additional fields required for Digital Product Passports. Built using [LinkML](https://linkml.io/), it generates multiple schema formats (JSON Schema, SHACL, OWL).

**Core Philosophy**: This model answers critical questions about product journey and lifecycle:
- **"Where has this product been?"** → Supply chain events and logistics tracking
- **"Who has handled it?"** → Ownership transfers and actor involvement
- **"What happened to it?"** → Value-adding activities and transformations
- **"How did it move?"** → Transportation events during supply chain journey
- **"Can it be returned?"** → Reverse logistics and end-of-life pathways

Static product information (identity, manufacturing origin, materials, initial import/export data) is handled by the Product Profile data model - this model focuses exclusively on time-stamped events and state changes that occur AFTER initial market entry.

### EPCIS Alignment Strategy

This model adopts and extends the **GS1 EPCIS 2.0 standard** event structure:
- **ObjectEvent** → Maps to our LogisticsEvents (observation of objects at locations)
- **TransactionEvent** → Maps to our OwnershipEvent (business transactions)
- **TransformationEvent** → Maps to our ValueAddingActivityLocation (inputs to outputs)
- **AggregationEvent** → Supported for packaging/unpacking operations
- **AssociationEvent** → Maps to our ActorTracking (entity associations)

Each event type inherits EPCIS's four dimensions (What, When, Where, Why) and extends them with DPP-specific requirements. This ensures:
- Direct import/export of EPCIS event data
- Compatibility with existing GS1 infrastructure
- Extensibility for circular economy requirements

### Why This Model Beyond Direct EPCIS Usage

**This model provides a DPP-optimized structure for EPCIS events, adding explicit support for reverse logistics (returns, refurbishment, recycling) which EPCIS handles weakly.** Rather than requiring implementers to interpret how EPCIS maps to DPP needs, this model provides that mapping explicitly with clear event categories for ownership transfers, value-adding activities, and end-of-life pathways that are critical for circular economy but not well-defined in standard EPCIS implementations.

### Core Hierarchy

```
TraceabilityLifecycleEvents (root)
├── 1. ProductHistory
│   ├── EventIdentifier
│   ├── EventTimestamp
│   ├── EventType
│   ├── EventLocation
│   ├── EventDescription
│   └── LinkedProductIdentifier
├── 2. OwnershipEvent
│   ├── TransferDate
│   ├── PreviousOwner (GLN, LEI, organization details)
│   ├── NewOwner (GLN, LEI, organization details)
│   ├── TransferType (sale, consignment, lease, donation)
│   ├── TransferLocation
│   ├── ChainOfCustodyReference
│   └── OwnershipDocumentation
├── 3. ValueAddingActivityLocation
│   ├── ActivityIdentifier
│   ├── ActivityType (assembly, processing, packaging, quality control)
│   ├── ActivityDate
│   ├── FacilityIdentifier (GLN, OSID)
│   ├── ProcessDescription
│   ├── InputProducts (components/materials consumed)
│   └── OutputProducts (resulting products)
├── 4. ActorTracking
│   ├── ActorIdentifier (GLN, LEI, VAT)
│   ├── ActorRole (manufacturer, distributor, retailer, service provider)
│   ├── ActivityTimestamp
│   ├── ActionPerformed
│   ├── ResponsibilityScope
│   └── CertificationStatus
├── 5. LogisticsEvents
│   ├── ShipmentIdentifier
│   ├── TransportMode (road, rail, sea, air, multimodal)
│   ├── DepartureLocation (UN/LOCODE)
│   ├── DepartureTimestamp
│   ├── ArrivalLocation (UN/LOCODE)
│   ├── ArrivalTimestamp
│   ├── CarrierIdentifier
│   ├── RouteInformation
│   ├── IntermediateStops
│   │   ├── StopLocation (UN/LOCODE)
│   │   ├── StopTimestamp
│   │   └── StopDuration
│   └── TransportConditions (temperature, humidity tracking)
└── 6. ReverseLogistics
    ├── ReturnEventIdentifier
    ├── ReturnInitiationDate
    ├── ReturnReason (defect, warranty, end-of-life, recall)
    ├── CustomerReturnChannels
    │   ├── ReturnLocation
    │   ├── ReturnMethod
    │   └── ReturnDocumentation
    ├── RefurbishmentEvent
    ├── RecyclingEvent
    └── DisposalEvent
```

### Workflow Sequence

#### **Step 1: ProductHistory**
Foundation for all lifecycle events with complete audit trail (EPCIS-compatible base structure):
- **EventIdentifier**: Unique identifier for each event (UUID format, maps to EPCIS eventID)
- **EventTimestamp**: ISO 8601 timestamp with timezone (EPCIS eventTime)
- **EventType**: EPCIS event types (ObjectEvent, TransactionEvent, TransformationEvent, etc.)
- **EventLocation**: GLN or URI for location (EPCIS readPoint/bizLocation)
- **EventDescription**: Human-readable event description (extends EPCIS)
- **LinkedProductIdentifier**: EPC URI or GTIN + serial (EPCIS epcList)
- **Disposition**: Business state (EPCIS disposition)
- **BizStep**: Business process step (EPCIS bizStep vocabulary)

#### **Step 2: OwnershipEvent** 
Legal ownership and custody transfers throughout supply chain (extends EPCIS TransactionEvent):
- **TransferDate**: Precise timestamp of ownership change (EPCIS eventTime)
- **PreviousOwner**: Source party GLN (EPCIS source)
- **NewOwner**: Destination party GLN (EPCIS destination)
- **TransferType**: EPCIS business transaction type (po/despatch_advice/invoice)
- **TransferLocation**: Physical location GLN (EPCIS bizLocation)
- **ChainOfCustodyReference**: Previous event links (EPCIS extensions)
- **OwnershipDocumentation**: Digital references (EPCIS bizTransactionList)
- **Action**: EPCIS action (ADD/OBSERVE/DELETE)

#### **Step 3: ValueAddingActivityLocation**
Transformation and processing events that modify or enhance products (EPCIS TransformationEvent):
- **ActivityIdentifier**: Unique activity reference (EPCIS eventID)
- **ActivityType**: EPCIS transformation type vocabulary
- **ActivityDate**: When occurred (EPCIS eventTime)
- **FacilityIdentifier**: Facility GLN (EPCIS bizLocation)
- **ProcessDescription**: Transformation details (EPCIS extensions)
- **InputProducts**: Input EPCs and quantities (EPCIS inputEPCList/inputQuantityList)
- **OutputProducts**: Output EPCs and quantities (EPCIS outputEPCList/outputQuantityList)
- **TransformationID**: Links related transformations (EPCIS transformationID)

#### **Step 4: ActorTracking**
All supply chain participants and their interactions with products:
- **ActorIdentifier**: Organization identifier (GLN/LEI/VAT)
- **ActorRole**: MANUFACTURER/DISTRIBUTOR/WHOLESALER/RETAILER/SERVICE_PROVIDER/TRANSPORTER
- **ActivityTimestamp**: When actor interacted with product
- **ActionPerformed**: Specific action taken
- **ResponsibilityScope**: Actor's responsibility boundaries
- **CertificationStatus**: Relevant certifications (ISO, organic, fair trade)

#### **Step 5: LogisticsEvents**
Supply chain transportation and movement tracking (post-import):
- **ShipmentIdentifier**: Unique shipment reference (carrier tracking number)
- **TransportMode**: ROAD/RAIL/SEA/AIR/MULTIMODAL/PIPELINE
- **DepartureLocation**: Origin UN/LOCODE (e.g., DEHAM for Hamburg)
- **DepartureTimestamp**: Departure time with timezone
- **ArrivalLocation**: Destination UN/LOCODE
- **ArrivalTimestamp**: Actual or estimated arrival
- **CarrierIdentifier**: Transport company GLN or identifier
- **RouteInformation**: Planned route details
- **IntermediateStops**: Distribution centers, warehouses, and transshipment points
- **TransportConditions**: Temperature, humidity, shock monitoring data
  
*Note: Initial import/export and customs data are captured in the Product Profile model. This tracks subsequent domestic and international movements within the supply chain.*

#### **Step 6: ReverseLogistics**
End-of-life and return pathway management:
- **ReturnEventIdentifier**: Unique return tracking ID
- **ReturnInitiationDate**: When return process started
- **ReturnReason**: DEFECT/WARRANTY_CLAIM/END_OF_LIFE/PRODUCT_RECALL/CUSTOMER_DISSATISFACTION
- **CustomerReturnChannels**: Available return methods and locations
- **RefurbishmentEvent**: Repair and refurbishment activities
- **RecyclingEvent**: Material recovery and recycling processes
- **DisposalEvent**: Final disposal method and certification

### Key Design Principles

1. **EPCIS Compliance**: Core event structure follows GS1 EPCIS 2.0 standard for maximum interoperability
2. **Event-Centric Architecture**: Every entry is a time-stamped event with clear before/after states
3. **Immutable Event Log**: Events are append-only, creating permanent audit trails
4. **Actor Attribution**: Every event links to responsible actors with verifiable identities
5. **Location Precision**: Events include precise location data using GS1 GLN and international standards
6. **Document Linkage**: Events reference supporting documentation without duplicating static data
7. **Standards-Based**: Native support for GS1 (EPCIS, GLN, GTIN), UN/LOCODE, and ISO standards
8. **Extensible**: EPCIS extension mechanism for DPP-specific requirements
9. **Complementary to Static Data**: Excludes information already captured in Product Profile (initial import/export, customs documentation, manufacturer details, static specifications)



### Data Properties

Each class has a corresponding value property (e.g., `name_value`, `company_id_value`) that holds the actual data. All value properties are string type except where specified otherwise.

#### SQL Identifiers

Every data point in the model includes a `sql_identifier` annotation that serves as a unique, machine-friendly database identifier. These identifiers follow a structured namespace pattern to ensure uniqueness across the entire data model:

**Pattern**: `trc_[category]_[specific_name]`

**Features:**
- **Traceability Prefix**: All identifiers start with `trc_` to clearly identify them as belonging to the Traceability and Life Cycle Events data model
- **Hierarchical Namespacing**: Uses category prefixes (`hist_`, `owner_`, `value_`, `actor_`, `logis_`, `reverse_`) to provide context and prevent naming conflicts
- **Database-Friendly**: Uses underscores and avoids special characters for SQL compatibility
- **Unique Across Model**: No duplicate identifiers, even when similar concepts appear in different parts of the hierarchy
- **Reasonable Length**: Abbreviated but meaningful names that balance clarity with practical database usage

**Examples:**
- `trc_hist_event_id` - Event identifier in Product History
- `trc_owner_transfer_date` - Transfer date in Ownership Events
- `trc_value_activity_type` - Activity type in Value-Adding Activities
- `trc_actor_gln` - Actor GLN in Actor Tracking
- `trc_logis_shipment_id` - Shipment identifier in Logistics Events
- `trc_reverse_return_reason` - Return reason in Reverse Logistics

This identifier system enables seamless integration with databases and ensures clear data model composition when combining with other CE-RISE data models.


---

## Development Roadmap

| Step | Component | Criticalities Identified | Solutions Implemented | Status | Missing/TODO |
|------|-----------|-------------------------|----------------------|--------|--------------|
| **1** | **ProductHistory** | • Need for comprehensive event tracking<br>• Lack of standardized event types<br>• Missing audit trail capabilities<br>• No link to product identities | • Implemented UUID-based event identifiers<br>• ISO 8601 timestamps with timezone support<br>• Standardized event type taxonomy<br>• GPS and facility-based location tracking<br>• Links to Product Profile identifiers | **IN PROGRESS** | • Event type ontology development<br>• Blockchain integration for immutability<br>• Event correlation engine |
| **2** | **OwnershipEvent** | • Complex custody chain tracking<br>• Legal ownership vs physical possession<br>• International transfer complications<br>• Document verification needs | • Dual owner identification (previous/new)<br>• Transfer type classification<br>• Chain of custody references<br>• Digital document linking<br>• GS1 GLN and LEI integration | **PLANNED** | • Smart contract integration<br>• Ownership verification APIs<br>• Multi-signature validation |
| **3** | **ValueAddingActivityLocation** | • Transformation tracking complexity<br>• Input/output product mapping<br>• Process documentation<br>• Quality control integration | • Activity type taxonomy<br>• Input/output product arrays<br>• Process description fields<br>• Facility identification via GLN/OSID<br>• Timestamp and location tracking | **PLANNED** | • Process optimization metrics<br>• Quality measurement integration<br>• Carbon footprint calculation |
| **4** | **ActorTracking** | • Multiple actor roles in supply chain<br>• Certification verification<br>• Responsibility boundaries<br>• Actor authentication | • Comprehensive role taxonomy<br>• Multiple identifier support (GLN/LEI/VAT)<br>• Certification status tracking<br>• Responsibility scope definition<br>• Time-stamped interactions | **PLANNED** | • Real-time actor verification<br>• Role-based access control<br>• Certification validation APIs |
| **5** | **LogisticsEvents** | • Multi-modal transport tracking<br>• Supply chain visibility<br>• Real-time location updates<br>• Transport condition monitoring | • UN/LOCODE location standards<br>• Multi-modal transport support<br>• Intermediate stops tracking<br>• Transport condition monitoring<br>• Route information capture<br>• Carrier identification<br>• Excludes initial import data (in Product Profile) | **PLANNED** | • IoT sensor integration<br>• Real-time tracking APIs<br>• Predictive arrival algorithms<br>• Carbon emission tracking |
| **6** | **ReverseLogistics** | • Return pathway complexity<br>• End-of-life tracking<br>• Circular economy requirements<br>• Refurbishment documentation | • Return reason classification<br>• Customer return channels<br>• Refurbishment event tracking<br>• Recycling event capture<br>• Disposal certification<br>• Complete return lifecycle | **PLANNED** | • Circular economy metrics<br>• Material recovery tracking<br>• Warranty management integration<br>• Environmental impact assessment |

### Integration Opportunities

- **GS1 EPCIS Infrastructure**: Direct compatibility with EPCIS 2.0 repositories and query interfaces
- **Event Streaming**: Apache Kafka, AWS Kinesis for real-time EPCIS event processing
- **Blockchain**: Immutable event logging via Hyperledger Fabric or Ethereum with EPCIS payloads
- **IoT Platforms**: Integration with transport condition sensors generating EPCIS sensor events
- **Standards Bodies**: Full GS1 EPCIS 2.0 compliance, CBV (Core Business Vocabulary), UN/CEFACT alignment
- **Analytics**: Time-series databases for event analysis, graph databases for EPCIS event chains
- **External APIs**: EPCIS capture/query interfaces, GS1 Digital Link resolvers, carrier tracking APIs
- **Existing Systems**: Import from legacy WMS/ERP systems via EPCIS adapters



---

## Accessing Previous Releases

If you want to view the files published for version `v1.2.0`, open:

```
https://codeberg.org/CE-RISE-models/traceability-and-life-cycle-events/src/tag/pages-v1.2.0/generated/
```

Files available in that directory typically include:

- schema.json
- shacl.ttl
- model.ttl
- index.html


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
