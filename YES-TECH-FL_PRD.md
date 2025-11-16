# PRODUCT REQUIREMENTS DOCUMENT (PRD)
## Farm-Level Yield Estimation & Loss Assessment System
### Enhanced YES-TECH for Very-High Resolution Crop Monitoring

**Document Version**: 1.0  
**Last Updated**: November 16, 2025  
**Status**: Draft for Stakeholder Review  
**Owner**: Department of Agriculture & Farmers Welfare (DA&FW) / Ministry of Agriculture  
**Tech Lead**: Technology Implementing Partner (TIP) Selection Committee

---

## 1. EXECUTIVE SUMMARY

### 1.1 Vision
Transform India's crop insurance ecosystem from **village-level to farm-level yield estimation** by integrating very-high spatial resolution (VHR) satellite and UAV imagery with hybrid AI models, enabling:
- **Objective, transparent claim assessments** at individual farm granularity
- **Real-time damage detection and quantification** post-calamity
- **Expedited insurance settlements** (15-30 days vs. current 60-90 days)
- **Enhanced farmer trust** through multi-source evidence (satellite + photos + models)

### 1.2 Problem Statement
Current YES-TECH implementation (Kharif 2023-2024) operates at **Insurance Unit (IU) / village level**, creating:
- **Spatial resolution mismatch**: Calamities (flood patches, hail corridors, disease zones) are localized to sub-plots; village-level aggregation masks within-field variability
- **Accuracy plateau**: 30-40% RMSE at IU level; insufficient for ±10-15% farm-level accuracy requirement for individual claim fairness
- **Temporal lag**: Mid-season and end-of-season reports 60-90 days post-calamity; farmers need real-time alerts
- **CCE dependency**: Still relies on sampling-based ground truth; introduces human bias and delay
- **Calamity localization gap**: Cannot pinpoint flooded extent, hail damage corridors, or disease outbreak zones

### 1.3 Proposed Solution
**Three-pillar hybrid modeling framework**:
1. **Semi-Physical Models** (enhanced YES-TECH): Downscaled to 20-30m resolution using VHR satellite fAPAR extraction
2. **Process-Based Crop Simulation** (DSSAT/ORYZA) with **Satellite Data Assimilation**: Real-time constraint of model predictions with satellite LAI observations
3. **Deep Learning Damage Detection** (CNNs): Localize flood/hail/disease/pest damage using pre/post-event imagery + CROPIC crowdsourced photos

**Integration Layer**: Seamless APIs with PMFBY, CROPIC, insurance platforms, and state government systems

### 1.4 Business Impact
| **Metric** | **Current** | **Target (12 months)** | **Improvement** |
|---|---|---|---|
| **Claim Settlement Speed** | 60-90 days | 15-30 days | 60-75% faster |
| **Yield Prediction RMSE** | ±25-35% | ±12-18% | 40-50% more accurate |
| **Model Confidence Score** | 60-70% | 85-92% | Higher farmer trust |
| **False Claim Escape Rate** | ~5% | <2% | Better fraud detection |
| **Cost per Claim Assessment** | Rs. 500-800 | Rs. 150-250 | 60-70% cost reduction |
| **Farmer Satisfaction (NPS)** | 6.5/10 | 8.5/10 | Ecosystem credibility |

---

## 2. PRODUCT OVERVIEW

### 2.1 Product Name & Codename
- **Official Name**: Yield Estimation System based on Technology - Farm Level Extension (YES-TECH-FL)
- **Codename**: "KRISHISAT" (Agriculture Satellite)
- **Governance**: Mahalanobis National Crop Forecast Centre (MNCFC) with PMFBY Implementation Committee oversight

### 2.2 Product Categories
- **Primary**: Precision Agriculture Technology + Crop Insurance Innovation
- **Secondary**: AgriTech SaaS Platform for Government
- **Tertiary**: Climate-Resilient Rural Finance Solution

### 2.3 Target Users

#### Direct Users
1. **Farmers**: 50,000-500,000 across 10 states (Kharif 2024 → nationwide by Kharif 2026)
2. **Insurance Companies**: AICIL, Reliance General, SBI Insurance, Bajaj, Cholamandalam (7 empaneled insurers)
3. **State Agriculture Departments**: Block-level officers for crop monitoring and intervention targeting
4. **Technology Implementing Partners (TIPs)**: Empaneled satellite/ML agencies (20-30 organizations)

#### Indirect Users
- Government actuaries (premium pricing based on real-time risk data)
- Reinsurance companies (portfolio risk assessment)
- NGOs / farmer producer organizations (agricultural advisory dissemination)
- Research institutions (field data for model refinement)

### 2.4 Geographic Scope

**Phase 1 (Months 1-6)**: Proof-of-Concept in 3-5 High-Risk Districts
- Uttar Pradesh: Kanpur Central (flood-prone, high PMFBY adoption)
- Bihar: Darbhanga (flood/drought alternating)
- Assam: Nagaon (monsoon crop losses)
- Karnataka: Belgaum (hail risk)
- Maharashtra: Vidarbha region (drought + pest)

**Phase 2 (Months 7-12)**: State-Level Scaling
- All YES-TECH Phase 1 Implementation States (Appendix 1 of YES-TECH Manual):
  - Andhra Pradesh, Assam, Haryana, Karnataka, Madhya Pradesh, Maharashtra, Odisha, Rajasthan, Tamil Nadu

**Phase 3 (Months 13-18)**: National Rollout
- Expand to all states under PMFBY coverage (28 states + 8 UTs)
- Estimated farm count: 500,000+ by Kharif 2026

### 2.5 Crop & Seasonal Focus

**Pilot Crops** (Phase 1):
- **Paddy (Rice)**: Kharif season (June-October)
  - Varieties: IR64, Pusa Basmati, BAS370, Aditya
  - Calamities: Flooding, early/late monsoon, pests (leaf folder, stem borer)
- **Wheat**: Rabi season (October-March)
  - Varieties: PBW, DBW, Lok-1
  - Calamities: Frost, hail, disease (rust, blight)

**Post-Pilot Expansion** (Phase 2):
- Soybean, Maize, Groundnut, Chickpea, Cotton (state-specific)

---

## 3. TECHNICAL REQUIREMENTS

### 3.1 Functional Requirements

#### FR-1: Very-High Resolution Data Ingestion & Processing

**Requirement**: Automated ingestion, preprocessing, and analysis of multispectral satellite and UAV data at <1m to 5m resolution

**Sub-Requirements**:

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-1.1 | Satellite Data APIs | Support Planet Labs, Maxar, Sentinel-2, Sentinel-1 via STAC protocol; automated daily retrieval for study area | P0 |
| FR-1.2 | Atmospheric Correction | FLAASH/6SV for L1B→L2A conversion; cloud masking (Fmask v4.4) | P0 |
| FR-1.3 | Pan-Sharpening | Merge Sentinel-1 SAR (10m) with optical (20-30m) for flood detection; preserve spatial detail | P1 |
| FR-1.4 | Ortho-Mosaicking | Auto-stitch UAV image sequences into georeferenced composites (RTK-GNSS, ±0.05m accuracy) | P1 |
| FR-1.5 | Temporal Interpolation | Fill cloud gaps using previous/future cloud-free acquisitions; Kalman smoothing for index time-series | P1 |
| FR-1.6 | Quality Assurance | Flag acquisitions with >30% cloud cover; skip if satellite overpass >5 days old; alert ops team | P0 |
| FR-1.7 | Data Versioning | Maintain immutable archive of all processed data products with metadata (acquisition date, processing version) | P1 |

**Acceptance Criteria**:
- Satellite data ingested within 24 hours of acquisition; UAV data within 12 hours of flight
- Cloud mask accuracy >90% (validated against Landsat Fmask benchmark)
- Ortho-mosaic RMS error <0.5m horizontal, <1m vertical

---

#### FR-2: Vegetation Index & Feature Extraction (20-30m Resolution)

**Requirement**: Compute standardized vegetation indices and spatial/spectral features for crop health monitoring

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-2.1 | Weekly Index Calculation | NDVI, GNDVI, LSWI, NDRE (red-edge), EVI at 20m resolution; 8-day composites for continuous time-series | P0 |
| FR-2.2 | Temporal Smoothing | Savitzky-Golay filter (window=5 weeks) to remove noise while preserving phenological transitions | P0 |
| FR-2.3 | Phenology Detection | Auto-detect sowing, peak greenness, and maturity weeks from NDVI progression; compare with crop calendar | P1 |
| FR-2.4 | Textural Features | GLCM (Gray-Level Co-occurrence Matrix) for canopy heterogeneity mapping; identify within-field variability zones | P1 |
| FR-2.5 | Spectral Unmixing | Linear unmixing to extract fAPAR directly from reflectance spectra (vs. coarse MODIS); PROSAIL inversion | P0 |
| FR-2.6 | fAPAR Time-Series | Daily/weekly fAPAR profiles as input to semi-physical model; uncertainty bounds ±0.05 from inversion error | P0 |

**Acceptance Criteria**:
- fAPAR extraction RMSE <0.08 vs. ground-truth radiometer measurements
- Phenology detection timing accuracy ±5 days (validated against CCE observations)
- Texture features capture within-field yield variability with R² >0.60

---

#### FR-3: Semi-Physical Yield Model (Enhanced YES-TECH)

**Requirement**: Downscale existing YES-TECH semi-physical model from 500m to 20-30m resolution using VHR satellite data

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-3.1 | Model Downscaling | Replace coarse MODIS fAPAR (500m) with high-res Planet/Sentinel fAPAR (5-20m); maintain physics-based PAR-biomass framework | P0 |
| FR-3.2 | Field-Specific RUE | Cluster historical CCE data by soil texture + variety; assign Radiation Use Efficiency per cluster (not single village value) | P0 |
| FR-3.3 | Water Stress Mapping | Per-pixel LSWI→water scalar; identify drought zones with <0.7 water availability factor | P0 |
| FR-3.4 | Sub-Field Variability | Generate 20×20m yield prediction grid; classify as high/medium/low-yield zones | P1 |
| FR-3.5 | Uncertainty Estimation | RMSE propagation from input data errors; output 90% confidence interval per pixel | P1 |
| FR-3.6 | Pre-Season Calibration | Assimilate 2 weeks of early-season satellite observations to refine initial RUE/HI estimates | P1 |

**Acceptance Criteria**:
- Field-level RMSE ±15-22% (vs. current ±25-35% at IU level)
- 90% CI coverage: 85-95% of validation fields fall within predicted bands
- Execution time <2 minutes per farm (for weekly updates across 50,000 farms)

---

#### FR-4: DSSAT Crop Simulation with Data Assimilation

**Requirement**: Integrate DSSAT-CERES/ORYZA models with satellite-based LAI assimilation for real-time yield forecasting

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-4.1 | Field Parameterization | Auto-populate from: soil health card database, farmer insurance app (sowing date, variety), IMD weather data | P0 |
| FR-4.2 | Genotype Coefficients | Pre-calibrated parameters for top 10 varieties per region; accuracy ±12% vs. CCE | P0 |
| FR-4.3 | Ensemble Kalman Filter | Weekly update of model states (LAI, biomass, stress) using satellite-derived LAI; reduce uncertainty 25-35% | P0 |
| FR-4.4 | Stress Diagnosis | Extract water/nitrogen stress factors; cross-validate with CROPIC photos and farmer field observations | P1 |
| FR-4.5 | Phenological Tracking | Compare model-predicted vs. satellite-observed phenology; flag if divergence >7 days | P1 |
| FR-4.6 | Pre-Harvest Forecast | 2-3 weeks before harvest, finalize yield prediction based on grain-filling simulation | P0 |
| FR-4.7 | Variety-Specific Response | Support early/late maturity cultivars, drought-tolerant genotypes; adjust parameters by farmer-declared variety | P1 |

**Acceptance Criteria**:
- R² = 0.72-0.82 for within-block yield predictions vs. CCE ground truth (2020-2024 historical data)
- RMSE ±18-25% at farm level
- Data assimilation reduces model uncertainty by >20% (quantified via ensemble spread)

---

#### FR-5: CNN-Based Damage Detection & Localization

**Requirement**: Automated identification and quantification of localized crop damage (flood, hail, disease, pest) using deep learning

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-5.1 | Flood Damage Detector | EfficientNetB7 fine-tuned on pre/post-event satellite imagery; binary classification (healthy vs. flooded) + probability map | P0 |
| FR-5.2 | Flood Extent Polygons | Generate flooded area polygons at farm level; calculate inundation % and corresponding yield loss | P0 |
| FR-5.3 | Training Dataset | Archive 2023-2024 PMFBY satellite imagery + manual labels (300-500 flood patches across states) | P0 |
| FR-5.4 | Inference Speed | <5 seconds per farm; support batch processing of 10,000+ farms/day during calamity events | P0 |
| FR-5.5 | Disease Detection | ResNet50 fine-tuned for blast, brown spot (rice), rust (wheat); support transfer learning on Plant Village dataset | P1 |
| FR-5.6 | Lodging Detection | Mask R-CNN for per-plant angle measurement; identify lodged vs. vertical plants; calculate lodging % | P1 |
| FR-5.7 | Hail Damage Detection | CNN classification of leaf perforation/scarring patterns; aggregate damage extent per field | P1 |
| FR-5.8 | CROPIC Integration | Consume crowdsourced photos; run damage classifier; return confidence scores for farmer visibility | P0 |
| FR-5.9 | Confidence Scoring | Output damage probability (0-100%); flag <70% confidence for manual verification | P0 |
| FR-5.10 | Explainability | Attention maps showing which image regions triggered damage classification; SHAP values for transparency | P2 |

**Acceptance Criteria**:
- Flood detection accuracy: 85-92% for >30% field-level inundation
- Disease/pest detection recall >80% (minimize false negatives); precision >75%
- CROPIC photo validation agreement: 80%+ of AI damage classifications match farmer/field officer assessments

---

#### FR-6: Ensemble Prediction Framework

**Requirement**: Combine semi-physical, DSSAT, and CNN predictions with adaptive weighting and uncertainty quantification

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-6.1 | Multi-Model Ensemble | Weighted average of 3 component models; weights dynamically adjust based on seasonal conditions & calamity detection | P0 |
| FR-6.2 | Adaptive Weighting Logic | Pre-season: 40/60% (SP/DSSAT); mid-season: 50/50%; post-calamity: 20/40/40% (SP/DSSAT/CNN) | P0 |
| FR-6.3 | Uncertainty Quantification | Ensemble standard deviation combining model uncertainties; 90% CI output | P0 |
| FR-6.4 | Confidence Classification | HIGH (CI <±15%), MEDIUM (±15-25%), LOW (>±25%) | P0 |
| FR-6.5 | Prediction Explainability | Breakdown: component model contributions, weight justification, uncertainty sources | P1 |
| FR-6.6 | Outlier Detection | Flag predictions with CI >50% of mean for manual review; investigate data quality issues | P1 |
| FR-6.7 | Recursive Calibration | Post-season: Compare predictions vs. actual harvest; update model weights for next season | P1 |

**Acceptance Criteria**:
- Ensemble RMSE ±12-18% at farm level (20-30% improvement vs. individual models)
- 90% CI coverage: 88-92% of validation fields within bands
- Farmer-reported yield vs. prediction scatter plot: R² >0.78

---

#### FR-7: Automated Claim Assessment Workflow

**Requirement**: Real-time, multi-evidence-based claim calculation with objective damage quantification

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-7.1 | Calamity Detection | Real-time satellite/UAV change detection + CROPIC photo upload → trigger claim pipeline automatically | P0 |
| FR-7.2 | Damage Quantification | CNN maps + geometry extraction → affected area (ha); combine with yield prediction for loss % | P0 |
| FR-7.3 | Yield Loss Calculation | Yield loss % = (Insured baseline - Predicted) / Insured × 100 | P0 |
| FR-7.4 | Claim Amount Derivation | Claim amount Rs. = (Yield loss % / 100) × Area (ha) × Sum Insured per ha | P0 |
| FR-7.5 | Multi-Source Evidence Compilation | PDF report: satellite damage map + pre/post-event photos + model prediction confidence + CCE baseline | P0 |
| FR-7.6 | Consistency Flagging | If damage extent ≠ yield loss (e.g., 50% damage but 10% yield drop), flag for review | P1 |
| FR-7.7 | Auto-Approval Logic | Approve if all confidence scores >85%, no consistency flags, damage amount within ±20% of historical district mean | P0 |
| FR-7.8 | Manual Review Queue | Flag 5-10% highest-risk claims; assign to insurance adjuster for field verification | P0 |
| FR-7.9 | Settlement Timeline | 7 days: auto-approved claims; 15 days: manual review + decision; 30 days: all claims disbursed | P0 |
| FR-7.10 | Payment Integration | Direct farmer account transfer via PMFBY banking partners; track reconciliation | P1 |

**Acceptance Criteria**:
- Claim settlement time: 15-30 days (vs. current 60-90 days)
- Auto-approval rate: 70-80% of claims processed without manual intervention
- Farmer satisfaction with transparency: 8/10 or higher (survey post-payout)

---

#### FR-8: Within-Field Variability Mapping & Visualization

**Requirement**: Generate actionable farm-level yield maps for farmer decision-support and insurance adjudication

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-8.1 | Yield Variability Map | 20×20m resolution grid; classified into high/medium/low zones with color-coded visualization | P0 |
| FR-8.2 | Web Dashboard Display | Interactive Leaflet map; zoom to farm level; toggle between yield, damage, vegetation indices, satellite imagery | P0 |
| FR-8.3 | Mobile App Integration | Push notifications to farmer: "Water stress detected in eastern 0.5 ha; recommend irrigation by Aug 15" | P1 |
| FR-8.4 | Downloadable Outputs | GeoTIFF format (Cloud-Optimized) for GIS import; CSV summary statistics; PDF reports | P1 |
| FR-8.5 | Historical Comparison | Overlay 3-year historical yield maps on current prediction for trend analysis | P2 |
| FR-8.6 | Farmer-Accessible Version | Simplified UI for non-technical farmers; limit to essential outputs (yield, damage, claim amount) | P1 |
| FR-8.7 | Block Officer Dashboard | Village-wise aggregated yield maps; calamity heatmaps; claim processing status pipeline | P1 |
| FR-8.8 | State Government Dashboard | Real-time monitoring; early warning for >30% predicted yield loss areas; budgeting forecasts | P1 |
| FR-8.9 | Insurance Company Dashboard | Claim portfolio view; fraud detection; premium risk assessment by geography/crop | P1 |

**Acceptance Criteria**:
- Dashboard load time <3 seconds (even with 50,000+ farms loaded)
- Map responsiveness on mobile devices (tested on 4G networks typical of rural India)
- Farmer engagement: 60%+ of farmer users access yield map at least once per season

---

#### FR-9: API & Integration Layer

**Requirement**: Seamless integration with PMFBY, CROPIC, insurance platforms, and state agriculture systems

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-9.1 | PMFBY Data Consumption | Read: Insured crop list, IU boundaries, CCE ground truth; Write: Farm-level predictions, damage alerts, claim recs | P0 |
| FR-9.2 | PMFBY API Specification | OpenAPI 3.0 spec; REST endpoints with JWT authentication; webhook callbacks for calamity events | P0 |
| FR-9.3 | CROPIC Photo Ingestion | Read crowdsourced photos (geotagged, timestamped); Run damage classifier; Write scores back to CROPIC UI | P0 |
| FR-9.4 | Insurance Company APIs | Push pre-filled claim forms with satellite/photo evidence; consume policy/claimant records | P1 |
| FR-9.5 | State Agri Portal Integration | Role-based access; separate views for farmer/block officer/auditor; downloadable reports | P1 |
| FR-9.6 | Data Governance & Security | Field-level encryption; audit trails; GDPR-like privacy compliance for farmer data | P1 |
| FR-9.7 | Rate Limiting & Scalability | Handle 10,000 concurrent API requests during peak calamity season; auto-scale backend | P1 |
| FR-9.8 | Backwards Compatibility | Support legacy PMFBY 2023 data formats; gradual migration to new YES-TECH-FL schema | P1 |

**Acceptance Criteria**:
- API uptime >99.5% (measured monthly)
- Data consistency: <2-hour lag between PMFBY updates and YES-TECH-FL system
- API documentation: Complete OpenAPI spec with example requests/responses

---

#### FR-10: Data Pipeline & Scheduling

**Requirement**: Automated, fault-tolerant data ingestion, processing, and model inference workflows

| **ID** | **Requirement** | **Details** | **Priority** |
|---|---|---|---|
| FR-10.1 | Weekly Processing Cycle | Satellite + model inference runs every 7 days during growing season (June-October for paddy) | P0 |
| FR-10.2 | Daily Post-Calamity Trigger | Real-time satellite change detection; UAV/drone flights within 24 hours if anomaly >30% detected | P0 |
| FR-10.3 | Airflow Orchestration | Apache Airflow DAGs; retry logic; failure notifications to ops team; manual intervention hooks | P0 |
| FR-10.4 | Data Quality Checks | Validate satellite data availability, weather data completeness, soil DB lookups | P0 |
| FR-10.5 | Fallback Protocols | If satellite unavailable >3 days: Use DSSAT-only predictions; if weather data missing: Interpolate from neighbors | P1 |
| FR-10.6 | Processing Log & Audit | Immutable audit trail of all computations; version tracking of models/data used | P1 |

**Acceptance Criteria**:
- Weekly pipeline completes in <6 hours (for 50,000 farms across 10 states)
- Calamity detection pipeline: <2 hours from satellite acquisition to farmer notification
- Zero data corruption; 100% audit trail consistency

---

### 3.2 Non-Functional Requirements

#### Performance Requirements

| **NFR-ID** | **Requirement** | **Target** | **Measurement** |
|---|---|---|---|
| **NFR-P1** | API Response Time | <2 sec (90th percentile) for single farm prediction | APM monitoring; load test with 1,000 concurrent requests |
| **NFR-P2** | Batch Processing Throughput | 50,000 farms / 6 hours | Airflow task completion time; throughput metrics |
| **NFR-P3** | Model Inference Latency | <5 sec per farm for CNN damage detection | GPU utilization monitoring; inference time logs |
| **NFR-P4** | Dashboard Load Time | <3 sec for 50,000+ farm data | Web analytics; RUM monitoring on Leaflet frontend |
| **NFR-P5** | Data Storage I/O | <50ms for GIS spatial queries (single farm extent) | CloudSQL/PostGIS query plans; index optimization |

#### Scalability Requirements

| **NFR-ID** | **Requirement** | **Target** | **Design Pattern** |
|---|---|---|---|
| **NFR-S1** | Horizontal Scalability | Support 10-500K farms without performance degradation | Kubernetes auto-scaling; sharded database by region |
| **NFR-S2** | Concurrent Users | 10,000+ simultaneous API requests during calamity season | Load balancer + CDN; Rate limiting per user tier |
| **NFR-S3** | Storage Growth | 100TB/year (satellite imagery, model outputs, audit logs) | Tiered storage (hot/warm/cold); S3 lifecycle policies |
| **NFR-S4** | Model Training Cycles | Weekly model retraining on new historical data | GPU cluster with auto-scaling; MLflow experiment tracking |

#### Reliability & Availability

| **NFR-ID** | **Requirement** | **Target** | **Mechanism** |
|---|---|---|---|
| **NFR-R1** | System Uptime | 99.5% (≤3.6 hours downtime/month) | Multi-region active-passive failover; RTO 1 hour |
| **NFR-R2** | Data Backup & Recovery | RTO 4 hours; RPO 1 hour (no data loss >1 hour window) | Continuous replication to secondary region; daily backups |
| **NFR-R3** | Graceful Degradation | If ML model service fails: Fall back to DSSAT-only predictions | Circuit breaker pattern; fallback API endpoints |
| **NFR-R4** | Disaster Recovery | Full system recovery within 24 hours post-catastrophic failure | Documented DR playbook; quarterly DR drills |

#### Security Requirements

| **NFR-ID** | **Requirement** | **Details** | **Compliance** |
|---|---|---|---|
| **NFR-SEC1** | Data Encryption | End-to-end encryption (TLS 1.3 in transit; AES-256 at rest) | NIST FIPS 140-2 |
| **NFR-SEC2** | Authentication | JWT-based API auth; SSO via government AADHAR/eKYC | OWASP Top 10 |
| **NFR-SEC3** | Authorization | Role-based access control (RBAC); farmer sees only own farms; officers see IU-level | GDPR Article 32 |
| **NFR-SEC4** | Audit Logging | Immutable, tamper-proof logs of all data access and modifications | SOC 2 Type II compliance |
| **NFR-SEC5** | Penetration Testing | Quarterly external pen-tests; annual VAPT assessment | DSCI guidelines |
| **NFR-SEC6** | Privacy | PII anonymization in test datasets; farmer data separated by state silos | Right to be forgotten (deletable after season+1 year) |

#### Data Quality Requirements

| **NFR-ID** | **Requirement** | **Target** | **Validation Method** |
|---|---|---|---|
| **NFR-DQ1** | Satellite Data Completeness | 95% cloud-free coverage within 5-day windows; <5% data gaps in time-series | Daily acquisition monitoring; gap detection alerts |
| **NFR-DQ2** | Metadata Accuracy | 100% of records have correct GPS coordinates (±10m) and acquisition timestamps | Spatial validation queries; temporal coherence checks |
| **NFR-DQ3** | Model Prediction Bounds | 90% of predictions fall within physically plausible yield ranges (10-80 q/ha for paddy) | Data distribution analysis; outlier flagging |
| **NFR-DQ4** | Historical Data Archival | 7-year retention of all processing artifacts (satellite data, model runs, CCE ground truth) | S3 immutable versioning; compliance audits |

---

## 4. USER STORIES & ACCEPTANCE CRITERIA

### 4.1 Farmer User Stories

#### US-F1: View Farm-Level Yield Prediction
**As a farmer**, I want to see a real-time prediction of my farm's yield during the growing season, so that I can plan irrigation and input purchases accordingly.

**Acceptance Criteria**:
- [ ] Access prediction via mobile app (with offline support)
- [ ] Receive push notification when significant yield loss predicted (>20%)
- [ ] View 90% confidence interval to understand prediction uncertainty
- [ ] Compare prediction to historical 3-year average for context
- [ ] Receive stress alerts: "Water stress in eastern plot; irrigate by Aug 15"
- [ ] Language: Hindi + regional language options

**Definition of Done**:
- [ ] Farmer UAT testing with 50 farmers across 5 districts
- [ ] NPS score ≥7/10 from farmer feedback surveys
- [ ] App load time on 4G networks <5 seconds

---

#### US-F2: Upload Crop Damage Photos via CROPIC App
**As a farmer**, I want to take photos of flood/hail damage in my field and upload them, so that the insurance system validates my claim objectively.

**Acceptance Criteria**:
- [ ] Mobile photo capture with auto-geotagging (GPS + timestamp)
- [ ] AI-based damage classification returns confidence score (<2 sec analysis)
- [ ] See AI assessment in app: "High damage detected (92% confidence); your claim will be prioritized"
- [ ] Receive real-time claim estimate before manual processing
- [ ] Photo metadata securely uploaded; no personal data exposed
- [ ] Support 4 photos per field per season (as per CROPIC standard)

**Definition of Done**:
- [ ] 500+ CROPIC photos collected and AI-classified in pilot phase
- [ ] 80%+ match between AI and field officer manual assessment
- [ ] FAT (Factory Acceptance Test) on Android 9+ and iOS 13+

---

#### US-F3: Receive Automated Claim Settlement
**As a farmer**, I want my insurance claim to be processed automatically based on satellite evidence, so that I receive payout within 15-30 days instead of 60-90 days.

**Acceptance Criteria**:
- [ ] Claim amount calculated automatically from yield loss + satellite damage map
- [ ] PDF report mailed to farmer showing evidence: satellite damage map, yield prediction, CROPIC photos
- [ ] Direct bank transfer to farmer account within 30 days of calamity report
- [ ] Claim status visible in mobile app: Approved / Under Review / Paid
- [ ] SMS notification in Hindi when claim disbursed
- [ ] No paperwork required if auto-approved; manual field verification only if flagged

**Definition of Done**:
- [ ] 200 end-to-end claim settlements completed in pilot phase
- [ ] 95%+ of auto-approved claims require no rework
- [ ] Average settlement time <20 days

---

### 4.2 Insurance Company User Stories

#### US-I1: Receive Pre-Filled Claim Recommendations
**As an insurance claims officer**, I want to receive automated claim recommendations with satellite + photo evidence, so that I can approve/reject claims faster and with higher confidence.

**Acceptance Criteria**:
- [ ] API sends claim recommendation JSON with: farm ID, predicted yield, damage extent, suggested claim %, supporting images
- [ ] Damage evidence: satellite damage map (GeoTIFF) + CROPIC photos (with AI damage scores)
- [ ] Yield prediction explanation: component model contributions, uncertainty bounds, confidence level
- [ ] Auto-approved claims flagged as "Ready to Disburse" (skip adjuster review)
- [ ] Flagged claims marked "Manual Review Needed" with reason (e.g., inconsistent damage/yield loss)
- [ ] Dashboard shows claim pipeline: X auto-approved, Y pending review, Z rejected this week

**Definition of Done**:
- [ ] Integration UAT with 2-3 insurance companies
- [ ] API latency <1 sec for claim recommendation delivery
- [ ] Dashboard shows 70%+ auto-approval rate by Rabi 2024

---

#### US-I2: Detect Fraudulent Claims via Satellite Evidence
**As a fraud analyst**, I want the system to flag suspicious claims where satellite/photo evidence contradicts farmer loss claim, so that I can investigate and prevent payout fraud.

**Acceptance Criteria**:
- [ ] Satellite damage detection > 30% but predicted yield loss < 10% → Flag as inconsistency
- [ ] CROPIC photo damage classification <70% confidence → Flag for manual adjuster review
- [ ] Claim amount >150% of district historical average → Escalate to risk management team
- [ ] Geospatial outlier detection: Nearby farms show <5% loss, but this farm claims 50% → Flag
- [ ] Generate fraud score (0-100%); >70% triggers mandatory field visit
- [ ] Audit trail: Log all flagged claims, reasons, outcome of investigation

**Definition of Done**:
- [ ] Fraud detection tested on 2023-2024 historical claims
- [ ] Precision >85% (minimize false positives); Recall >75% (catch most actual fraud)
- [ ] 1-2 fraud rings per district identified and reported to investigation team

---

#### US-I3: Analyze Portfolio Risk & Premium Actuarials
**As an actuary**, I want to access real-time yield maps and loss distributions across districts, so that I can adjust premiums and reinsurance risk assessments.

**Acceptance Criteria**:
- [ ] Download district-level yield distribution (histogram) for each crop/season
- [ ] Spatial hotspot analysis: Identify high-loss villages; assess if premium adjustment needed
- [ ] Correlation analysis: Yield loss vs. rainfall/temperature data (climate risk factor)
- [ ] Trend dashboard: 3-year yield comparison; climate impact assessment
- [ ] Export to actuarial software (Excel, R-compatible CSV)
- [ ] Confidence in data: Prediction RMSE ±12-18% stated clearly in reports

**Definition of Done**:
- [ ] Actuarial team dashboard deployed and UAT passed
- [ ] 5+ premium adjustment recommendations generated based on satellite yield insights

---

### 4.3 State Government User Stories

#### US-G1: Monitor Crop Status in Real-Time via State Dashboard
**As a block-level agricultural officer**, I want to see village-wise crop yield maps and calamity alerts, so that I can identify districts needing emergency intervention.

**Acceptance Criteria**:
- [ ] Dashboard shows heatmap: Green (normal yield), Yellow (mild stress), Red (>30% yield loss)
- [ ] Calamity alert: SMS + email notification when >50% of farms in block show >30% yield loss on same day
- [ ] Drill-down: Block → Village → Farm level detail
- [ ] Download village-level aggregated yield statistics (CSV)
- [ ] Early warning: 2-week advance notice of drought/flood risk based on weather forecast + model prediction
- [ ] Offline-capable dashboard (critical areas may have poor connectivity)

**Definition of Done**:
- [ ] 100 block officers trained on dashboard usage
- [ ] 90% adoption rate (regular login/usage within 1 week of season start)
- [ ] NPS score ≥7/10 from officer feedback

---

#### US-G2: Generate Emergency Relief Targeting Data
**As a state government disaster management officer**, I want to identify exact villages/farms for emergency assistance distribution, so that relief reaches most affected areas efficiently.

**Acceptance Criteria**:
- [ ] System exports list: Top 20% most-affected farms by district (ranked by yield loss %)
- [ ] Map visualization: Calamity polygon overlay on administrative boundaries
- [ ] Recommended relief: "250 farmers in Kanpur Central need immediate irrigation support; cost estimate Rs. 5 crores"
- [ ] Integration with state portal: Data feeds into PM-FASAL and PMNRF (Pradhan Mantri National Relief Fund) systems
- [ ] Data refresh: Updated daily during calamity season
- [ ] Transparency: Data auditable; no individual farmer visibility without consent (privacy-first)

**Definition of Done**:
- [ ] Pilot relief operation based on YES-TECH-FL data in 1 district
- [ ] 80%+ of targeted farms confirmed as affected post-verification
- [ ] Cost efficiency: Relief distributed with <5% administrative overhead

---

## 5. PRODUCT ROADMAP

### Phase 1: Proof of Concept (Months 1-6)
**Goal**: Validate farm-level model accuracy and user acceptance in 3-5 pilot districts

**Deliverables**:
- Core ML pipeline: Semi-physical + DSSAT + CNN ensemble
- Web dashboard (backend API + basic frontend)
- 50 test farms with yield predictions + actual harvest validation (CCE comparison)
- API specification for PMFBY integration (draft)
- Training datasets: 300-500 labeled satellite damage images for CNN

**Milestones**:
- M1 (Month 1): Data pipeline & satellite ingestion MVP
- M2 (Month 2): Semi-physical model downscaling & fAPAR extraction
- M3 (Month 3): DSSAT integration + data assimilation setup
- M4 (Month 4): CNN damage detection model training & validation
- M5 (Month 5): Ensemble framework + API design
- M6 (Month 6): User testing & requirements refinement

**Success Metrics**:
- Field-level yield RMSE ±12-18% (vs. CCE ground truth)
- Model confidence score ≥80% on validation set
- Farmer satisfaction NPS ≥6/10 (pilot cohort)

---

### Phase 2: State-Level Scaling (Months 7-12)
**Goal**: Expand to 1-2 complete states; achieve production-ready reliability; integrate CROPIC + PMFBY

**Deliverables**:
- Production-grade system deployment on GCP/AWS
- CROPIC integration: Crowdsourced photo AI analysis
- PMFBY API live: Read insured farm data, write predictions + claim recommendations
- Mobile app (iOS + Android) with push notifications
- Insurance company dashboards for claim processing
- 50,000+ farms monitored across 1-2 states
- Genotype coefficient calibration for top 10 varieties per state

**Milestones**:
- M7 (Month 7): Cloud infrastructure setup + model containerization
- M8 (Month 8): CROPIC API integration + photo damage classifier
- M9 (Month 9): PMFBY integration UAT + data migration
- M10 (Month 10): Mobile app launch + farmer onboarding
- M11 (Month 11): Insurance company dashboard + claim workflow UAT
- M12 (Month 12): Full-season production run; post-harvest validation

**Success Metrics**:
- System uptime ≥99.5% during growing season
- API response time <2 sec (90th percentile)
- 70%+ of claims auto-approved without manual review
- Claim settlement time ≤20 days average
- Farmer engagement: ≥60% of users access mobile app ≥2x per season

---

### Phase 3: National Rollout (Months 13-18)
**Goal**: Deploy to all 10 YES-TECH Phase 1 states; achieve ≥85% farmer satisfaction; establish product sustainability

**Deliverables**:
- Full national deployment: 500,000+ farms across 10 states
- Post-harvest farmer survey: Yield prediction accuracy validation against actual harvest
- Model refinement: Calibrate to each state/crop combination; update next season
- Government adoption: Integration with state PM-FASAL portals; disaster relief workflows
- Policy paper: Ministry recommendation for YES-TECH-FL as standard practice (replace CCE for non-dispute claims)
- Technology partner ecosystem: Capacity building for 20-30 TIPs across India

**Milestones**:
- M13 (Month 13): Expand to 5 additional states
- M14 (Month 14): Multi-language support (Hindi, regional languages)
- M15 (Month 15): Post-harvest data collection & model retraining
- M16 (Month 16): District-level sustainability workshop (train-the-trainer)
- M17 (Month 17): Policy engagement; recommendation paper publication
- M18 (Month 18): Handover to Mahalanobis Centre for sustained operations

**Success Metrics**:
- Farmer NPS ≥8/10 (nationwide satisfaction)
- Prediction accuracy vs. actual yield: R² >0.78 (cross-validation)
- Cost per claim: ≤Rs. 200 (vs. current Rs. 500-800)
- Model confidence: ≥85% on 80%+ of predictions
- Insurance settlement time: ≤30 days for 95% of claims

---

## 6. RESOURCE PLAN

### 6.1 Team Structure

**Core Product Team** (Central):
- **Product Manager** (1): Overall vision, stakeholder alignment, roadmap
- **Tech Lead / Architect** (1): System design, cloud infrastructure, vendor management
- **Senior ML Engineer** (2): Model development, data science, algorithm R&D
- **Backend Engineer** (2): API development, database design, data pipeline
- **Frontend Engineer** (1): Web dashboard, mobile app UI/UX
- **DevOps / Cloud Specialist** (1): CI/CD, deployment, monitoring, security
- **QA Engineer** (1): Testing, UAT coordination, performance benchmarking
- **Data Scientist - Domain Expert** (1): Crop science, model interpretation, agronomic validation

**Supporting Teams**:
- **Agronomist Consultant** (0.5 FTE): DSSAT parameterization, crop variety calibration
- **Remote Sensing Specialist** (0.5 FTE): Satellite data preprocessing, vegetation index derivation
- **GIS/Spatial Analytics** (0.5 FTE): Geospatial visualization, polygon operations
- **Stakeholder Engagement Manager** (1): Insurance company coordination, state government liaison, farmer communication

**Total Core Team**: ~10-12 FTE

---

### 6.2 Cost Estimate (Annual, for 50,000 farms across 10 states)

| **Component** | **Cost (Rs. Lakhs)** | **Details** |
|---|---|---|
| **Personnel (Development + Ops)** | 100-120 | 10-12 core team members + 2-3 contractors |
| **Cloud Infrastructure (GCP/AWS)** | 40-50 | Compute, storage, bandwidth, managed services |
| **Satellite Data Acquisition** | 20-30 | Planet Labs daily, Maxar premium, Sentinel free |
| **UAV/Drone Operations** | 15-20 | Block-level pilots for 100-150 villages; sensor maintenance |
| **ML Model Training & Inference** | 15-20 | GPU clusters, MLflow, training data annotation |
| **Stakeholder Engagement** | 10-15 | Training, workshops, awareness campaigns |
| **Contingency (15%)** | 30-35 | Unforeseen costs, scope changes |
| **TOTAL ANNUAL COST** | **230-290** | ~**Rs. 2.3-2.9 Crores/year** |

**Cost per Farm**: ~Rs. 460-580/farm/season (vs. current CCE cost of Rs. 500-800/claim)

---

## 7. GOVERNANCE & OVERSIGHT

### 7.1 Decision-Making Structure

**Steering Committee** (Quarterly Meetings):
- Secretary, DA&FW (Chair)
- CEO, PMFBY
- Director, MNCFC
- Representative from AICIL + 1 other insurance company
- State Chief Secretary (rotating participation)

**Responsibilities**:
- Approve major scope/timeline changes
- Address policy blockers
- Allocate contingency budget
- Review risk register

**Product Backlog Committee** (Bi-weekly):
- Product Manager, Tech Lead, 1 insurance company rep, 1 state government rep
- Prioritize features, review sprint progress, manage scope

**Quality & Compliance** (Weekly):
- QA Lead, DevOps, Data Security Officer
- Verify compliance with GDPR-like privacy rules, SOC 2 controls, data quality thresholds

---

### 7.2 Success Criteria & KPIs

| **KPI** | **Baseline** | **Target (12 mo)** | **Measurement** |
|---|---|---|---|
| **Claim Settlement Speed** | 60-90 days | ≤30 days (95% of claims) | Audit daily claim processing logs |
| **Yield Prediction Accuracy** | RMSE ±25-35% | RMSE ±12-18% | Compare predictions vs. post-harvest CCE |
| **Model Confidence Score** | 60-70% | ≥85% (80% of predictions) | Classify confidence level; track distribution |
| **Farmer Satisfaction (NPS)** | 6.5/10 | ≥8.0/10 | Quarterly farmer surveys |
| **Cost per Claim** | Rs. 500-800 | ≤Rs. 250 | Track internal + system costs |
| **Auto-Approval Rate** | 0% | 70-75% of claims | Monitor claim processing pipeline |
| **System Uptime** | N/A | ≥99.5% | Continuous monitoring; alert on breaches |
| **Farms Monitored** | 0 | 50,000+ (Phase 1-2) | Track active farm registrations |
| **Insurance Company Adoption** | 0 | 5-7 insurers integrated | API usage metrics; claim submission counts |

---

## 8. RISKS & MITIGATION STRATEGIES

| **Risk** | **Impact** | **Probability** | **Mitigation** |
|---|---|---|---|
| **Cloud cost overruns** | Budget 50% over estimate | Medium | Implement cost optimization layer; negotiate enterprise rates; hybrid cloud option |
| **Satellite data unavailability (cloud cover)** | 2-week gaps in yield prediction | High | Pre-position fallback: SAR data (Sentinel-1); DSSAT-only predictions; conservative flag |
| **Model accuracy below target** | Farmer/insurer distrust | Medium | Invest in better training data (300-500 calamity samples); ensemble diversity; continuous retraining |
| **Stakeholder resistance** | Delayed adoption; new CCE verification system remains parallel | Medium | Early engagement workshops; transparent communication of benefits; voluntary opt-in pilot |
| **Data privacy breaches** | Farmer data exposure; government liability | Low | Encryption (AES-256), RBAC, audit logs, regular penetration testing, cyber insurance |
| **Farmer literacy barriers** | Low mobile app adoption | Medium | Simplified UI; SMS notifications; local language support; agent-mediated access in remote areas |
| **Integration delays with PMFBY** | System launch slippage 3-6 months | Medium | Establish PMFBY API early; dedicated liaison; weekly sync with CPMU-PMFBY team |
| **Regulatory changes** | New insurance guidelines contradict YES-TECH-FL approach | Low | Legal team monitors policy; early draft feedback to Ministry; contingency plan for adaption |

---

## 9. SUCCESS CRITERIA & GO/NO-GO DECISION GATES

### Phase 1 Exit Criteria (Month 6)
**GO if**:
- [ ] Field-level RMSE ±12-20% achieved (vs. ±25-35% current)
- [ ] 50+ farms validated end-to-end; R² >0.70 with CCE ground truth
- [ ] Farmer satisfaction NPS ≥6/10
- [ ] API specification finalized; PMFBY team sign-off received
- [ ] Budget within ±10% of plan

**NO-GO if**:
- [ ] RMSE >25% or R² <0.60 (model accuracy target missed)
- [ ] Farmer NPS <5/10 (low satisfaction)
- [ ] Major technical blocker unsolved (e.g., satellite data integration unfeasible)
- [ ] Budget overrun >20%

### Phase 2 Exit Criteria (Month 12)
**GO if**:
- [ ] System uptime ≥99% (vs. SLA target 99.5%; acceptable for Phase 2)
- [ ] 50,000 farms monitored across 1-2 states
- [ ] 70%+ claims auto-approved; settlement time ≤20 days
- [ ] PMFBY + CROPIC + Insurance API integration UAT passed
- [ ] Farmer app download rate >30,000; weekly active users >15,000

**NO-GO if**:
- [ ] System crashes >1 per week (production instability)
- [ ] Claims auto-approval <50% (workflow not effective)
- [ ] Insurance companies report >5% false positives on auto-approved claims
- [ ] PMFBY integration fails UAT (unmet data contract requirements)

### Phase 3 Exit Criteria (Month 18)
**GO for National Scale-Up if**:
- [ ] 500,000+ farms monitored; uptime ≥99.5%
- [ ] Farmer NPS ≥8/10 (high satisfaction)
- [ ] Prediction accuracy: R² >0.78 vs. actual harvest (cross-validation)
- [ ] Cost per claim: ≤Rs. 200 (target achieved)
- [ ] 5-7 insurance companies processing claims via YES-TECH-FL
- [ ] State government dashboards in production; 100+ officers trained

**NO-GO for National Scale-Up if**:
- [ ] Farmer NPS <7/10 (product not meeting customer needs)
- [ ] Prediction accuracy regression: R² <0.70
- [ ] Major security incident or data privacy breach
- [ ] Only 1-2 insurance companies adopted (market adoption risk)

---

## 10. APPENDICES

### Appendix A: Glossary

| **Term** | **Definition** |
|---|---|
| **CCE** | Crop Cutting Experiment; manual ground-based yield sampling method |
| **DSSAT** | Decision Support System for Agrotechnology Transfer; crop simulation model |
| **fAPAR** | Fraction of Absorbed Photosynthetically Active Radiation |
| **EnKF** | Ensemble Kalman Filter; data assimilation technique |
| **IU** | Insurance Unit (village or administrative block for PMFBY) |
| **LSWI** | Land Surface Water Index; vegetation water stress indicator |
| **NDVI** | Normalized Difference Vegetation Index; canopy greenness |
| **PAR** | Photosynthetically Active Radiation |
| **RUE** | Radiation Use Efficiency; crop's ability to convert light to biomass |
| **STAC** | SpatioTemporal Asset Catalog; standardized satellite data format |
| **TIP** | Technology Implementing Partner; empaneled satellite/ML agency |
| **VHR** | Very-High Resolution satellite data (<5m spatial resolution) |

### Appendix B: Reference Documents

1. YES-TECH Manual 2023 (Ministry of Agriculture, DA&FW)
2. PMFBY Operational Guidelines 2023
3. CROPIC Initiative Overview (Ministry of Agriculture)
4. Sentinel-2 Technical Guide (ESA)
5. DSSAT-CERES-Rice/Wheat Model Documentation
6. EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks (Tan & Le, 2019)
7. Data Assimilation: Methods, Algorithms, and Applications (Evensen, 2009)

### Appendix C: Technology Stack

**Backend**:
- Language: Python 3.10+
- API Framework: FastAPI
- Database: PostgreSQL + PostGIS (spatial queries)
- Cache: Redis
- Job Queue: Celery + RabbitMQ
- Orchestration: Apache Airflow

**ML/Data**:
- Satellite: Google Earth Engine API, Rasterio, Geopandas
- Models: TensorFlow/PyTorch (CNNs), DSSAT (process-based)
- Data Science: Scikit-learn, NumPy, Pandas, Xarray
- Visualization: Matplotlib, Plotly

**Cloud**:
- GCP: Vertex AI, BigQuery, Cloud Storage, Cloud Run, Compute Engine
- OR AWS: SageMaker, Redshift, S3, Lambda, EC2

**Frontend**:
- Web: React.js + TypeScript, Leaflet (mapping), Recharts (data viz)
- Mobile: React Native or Flutter (iOS + Android)
- DevOps: Docker, Kubernetes, Terraform

---

## 11. SIGN-OFF

| **Role** | **Name** | **Signature** | **Date** |
|---|---|---|---|
| **Product Manager** | [Name] | _______ | _______ |
| **Tech Lead** | [Name] | _______ | _______ |
| **Ministry of Agriculture, DA&FW** | [Secretary/CEO PMFBY] | _______ | _______ |
| **Steering Committee Chair** | [Secretary, DA&FW] | _______ | _______ |

---

**Document Distribution**:
- Steering Committee (quarterly reviews)
- MNCFC project team (weekly working)
- Technology partners/TIPs (upon empanelment)
- Insurance company stakeholders (upon partnership agreement)
- State agriculture departments (upon Phase 2 onboarding)

**Version History**:
- v1.0 (Nov 16, 2025): Initial PRD for stakeholder review
- v1.1 (TBD): Post-feedback refinement
- v2.0 (TBD): Final approved version for procurement

---

**END OF DOCUMENT**
