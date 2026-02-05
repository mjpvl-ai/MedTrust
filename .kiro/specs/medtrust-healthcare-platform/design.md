# Design Document: MedTrust Healthcare Intelligence Platform

## Overview

MedTrust is a comprehensive AI-powered healthcare intelligence platform that creates a trusted collaborative ecosystem between patients, doctors, and government medical bodies. The system employs a local-first, privacy-preserving architecture where sensitive medical data is processed on-device by default, with optional cloud enhancement for advanced AI capabilities.

The platform addresses three critical healthcare challenges:
1. **Patient Understanding**: Patients struggle to read and understand prescriptions, leading to medication errors and poor adherence
2. **Doctor Insights**: Doctors lack real-world data on treatment effectiveness and patient compliance between visits
3. **Evidence Gap**: Healthcare decisions often lack integration with latest research and official guidelines

### Key Design Principles

- **Privacy-First**: All processing happens locally by default; cloud is opt-in
- **Trust Triangle**: Three-way collaboration between patients, doctors, and government bodies
- **Evidence-Based**: Every recommendation backed by official guidelines or peer-reviewed research
- **Accessibility**: Works for all Indians regardless of language, literacy, or connectivity
- **Offline-Capable**: Core features work without internet
- **Scalable**: Designed to serve millions of users across India

## Architecture

### High-Level System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        PATIENT LAYER                            │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │           Mobile Application (React Native)              │   │
│  │  • Camera Integration  • Local Storage  • UI/UX         │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              ↕                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              LOCAL PROCESSING ENGINE                     │   │
│  │  • Tesseract OCR  • SQLite DB  • On-device ML           │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              ↕ (Optional, Encrypted)
┌─────────────────────────────────────────────────────────────────┐
│                         CLOUD LAYER (AWS)                       │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                    API Gateway                           │   │
│  │              (Authentication, Rate Limiting)             │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              ↕                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              PROCESSING SERVICES (Lambda)                │   │
│  │  • Advanced OCR  • AI Explanations  • Analytics         │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              ↕                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                  AI SERVICES                             │   │
│  │  • Amazon Bedrock (Claude)  • Amazon Textract           │   │
│  │  • Amazon Comprehend Medical                            │   │
│  └──────────────────────────────────────────────────────────┘   │
│                              ↕                                  │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              DATA LAYER                                  │   │
│  │  • S3 (Encrypted)  • DynamoDB  • RDS (PostgreSQL)       │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────────┐
│                    GOVERNMENT DATA LAYER                        │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │         External Data Integration Services               │   │
│  │  • CDSCO API  • ICMR Guidelines  • NPPA Pricing         │   │
│  │  • PubMed API  • Research Databases                     │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                              ↕
┌─────────────────────────────────────────────────────────────────┐
│                       DOCTOR LAYER                              │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              Web Dashboard (React)                       │   │
│  │  • Patient Monitoring  • Research Access  • Analytics   │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```


### Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      AWS CLOUD INFRASTRUCTURE                   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    REGION: ap-south-1 (Mumbai)          │   │
│  │                                                         │   │
│  │  ┌──────────────────────────────────────────────────┐  │   │
│  │  │         VPC (Virtual Private Cloud)              │  │   │
│  │  │                                                  │  │   │
│  │  │  ┌────────────────┐    ┌────────────────┐      │  │   │
│  │  │  │  Public Subnet │    │  Public Subnet │      │  │   │
│  │  │  │  AZ-1          │    │  AZ-2          │      │  │   │
│  │  │  │  • API Gateway │    │  • API Gateway │      │  │   │
│  │  │  │  • ALB         │    │  • ALB         │      │  │   │
│  │  │  └────────────────┘    └────────────────┘      │  │   │
│  │  │                                                  │  │   │
│  │  │  ┌────────────────┐    ┌────────────────┐      │  │   │
│  │  │  │ Private Subnet │    │ Private Subnet │      │  │   │
│  │  │  │  AZ-1          │    │  AZ-2          │      │  │   │
│  │  │  │  • Lambda      │    │  • Lambda      │      │  │   │
│  │  │  │  • ECS Tasks   │    │  • ECS Tasks   │      │  │   │
│  │  │  └────────────────┘    └────────────────┘      │  │   │
│  │  │                                                  │  │   │
│  │  │  ┌────────────────┐    ┌────────────────┐      │  │   │
│  │  │  │  Data Subnet   │    │  Data Subnet   │      │  │   │
│  │  │  │  AZ-1          │    │  AZ-2          │      │  │   │
│  │  │  │  • RDS Primary │    │  • RDS Replica │      │  │   │
│  │  │  │  • ElastiCache │    │  • ElastiCache │      │  │   │
│  │  │  └────────────────┘    └────────────────┘      │  │   │
│  │  └──────────────────────────────────────────────────┘  │   │
│  └─────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │              MANAGED SERVICES (Multi-AZ)                │   │
│  │  • S3 (Encrypted Storage)                               │   │
│  │  • DynamoDB (Global Tables)                             │   │
│  │  • Amazon Bedrock (Claude 3)                            │   │
│  │  • Amazon Textract                                      │   │
│  │  • Amazon Comprehend Medical                            │   │
│  │  • AWS KMS (Key Management)                             │   │
│  │  • CloudWatch (Monitoring)                              │   │
│  │  • CloudFront (CDN)                                     │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```


## Components and Interfaces

### 1. Mobile Application (Patient Interface)

**Technology Stack:**
- React Native (cross-platform iOS/Android)
- TypeScript for type safety
- Redux for state management
- React Native Camera for prescription capture
- SQLite for local database
- AsyncStorage for encrypted key-value storage
- React Native Voice for voice commands
- i18next for multilingual support

**Key Components:**

#### 1.1 Prescription Scanner Module
```typescript
interface PrescriptionScannerProps {
  onScanComplete: (result: ScanResult) => void;
  mode: 'local' | 'cloud';
}

interface ScanResult {
  imageUri: string;
  extractedText: string;
  confidence: number;
  structuredData: PrescriptionData;
  processingMode: 'local' | 'cloud';
  timestamp: Date;
}

interface PrescriptionData {
  patientName?: string;
  doctorName?: string;
  date?: Date;
  diagnosis?: string;
  medications: Medication[];
  instructions?: string;
}

interface Medication {
  name: string;
  genericName?: string;
  dosage: string;
  frequency: string;
  duration?: string;
  instructions?: string;
}
```

#### 1.2 Local OCR Engine
```typescript
interface LocalOCREngine {
  extractText(imageUri: string): Promise<OCRResult>;
  getConfidence(): number;
  supportedLanguages: string[];
}

interface OCRResult {
  text: string;
  confidence: number;
  boundingBoxes: BoundingBox[];
  processingTime: number;
}

interface BoundingBox {
  text: string;
  x: number;
  y: number;
  width: number;
  height: number;
  confidence: number;
}
```

#### 1.3 Medicine Database Module
```typescript
interface MedicineDatabase {
  searchMedicine(name: string): Promise<Medicine[]>;
  getMedicineById(id: string): Promise<Medicine>;
  getGenericAlternatives(medicineId: string): Promise<Medicine[]>;
  checkInteractions(medicineIds: string[]): Promise<Interaction[]>;
  updateDatabase(): Promise<void>;
}

interface Medicine {
  id: string;
  name: string;
  genericName: string;
  manufacturer: string;
  activeIngredient: string;
  strength: string;
  form: string; // tablet, capsule, syrup, etc.
  mrp: number;
  cdscoapproved: boolean;
  approvalDate: Date;
  category: string;
  prescriptionRequired: boolean;
  commonUses: string[];
  sideEffects: string[];
  contraindications: string[];
  pregnancyCategory?: string;
  storageInstructions: string;
}

interface Interaction {
  medicine1: string;
  medicine2: string;
  severity: 'mild' | 'moderate' | 'severe';
  description: string;
  recommendation: string;
  source: string;
}
```

#### 1.4 Explanation Generator
```typescript
interface ExplanationGenerator {
  generateSimpleExplanation(
    prescription: PrescriptionData,
    language: string,
    mode: 'local' | 'cloud'
  ): Promise<Explanation>;
}

interface Explanation {
  conditionExplanation: string;
  medicineExplanations: MedicineExplanation[];
  visualSchedule: MedicationSchedule;
  warnings: Warning[];
  costAnalysis: CostAnalysis;
  governmentVerification: GovernmentVerification;
}

interface MedicineExplanation {
  medicineName: string;
  purpose: string;
  howItWorks: string;
  whenToTake: string;
  sideEffects: string[];
  precautions: string[];
  visualAid?: string; // URL to icon/image
}

interface MedicationSchedule {
  medicines: ScheduledMedicine[];
  visualRepresentation: string; // SVG or image URL
}

interface ScheduledMedicine {
  name: string;
  times: MedicationTime[];
  withFood: boolean;
  specialInstructions?: string;
}

interface MedicationTime {
  time: string; // "08:00", "14:00", "20:00"
  icon: 'morning' | 'afternoon' | 'evening' | 'night';
  dosage: string;
}

interface Warning {
  type: 'interaction' | 'contraindication' | 'safety' | 'guideline';
  severity: 'info' | 'warning' | 'critical';
  message: string;
  action: string;
}

interface CostAnalysis {
  totalMonthlyCost: number;
  genericAlternatives: GenericAlternative[];
  potentialSavings: number;
  janAushadhiAvailability: PharmacyLocation[];
}

interface GenericAlternative {
  brandedName: string;
  brandedCost: number;
  genericName: string;
  genericCost: number;
  savings: number;
  savingsPercentage: number;
}

interface PharmacyLocation {
  name: string;
  address: string;
  distance: number;
  availableMedicines: string[];
  pricing: { [medicineName: string]: number };
}

interface GovernmentVerification {
  cdscoapproved: boolean;
  icmrGuidelineMatch: boolean;
  guidelineReference?: string;
  lastUpdated: Date;
  verificationBadge: 'verified' | 'partial' | 'not-verified';
}
```


#### 1.5 Health Tracking Module
```typescript
interface HealthTracker {
  logParameter(entry: HealthParameterEntry): Promise<void>;
  getHistory(
    parameter: HealthParameterType,
    startDate: Date,
    endDate: Date
  ): Promise<HealthParameterEntry[]>;
  getTrends(parameter: HealthParameterType): Promise<Trend>;
  generateReport(startDate: Date, endDate: Date): Promise<HealthReport>;
}

type HealthParameterType = 
  | 'blood_pressure'
  | 'blood_sugar'
  | 'weight'
  | 'temperature'
  | 'heart_rate'
  | 'oxygen_saturation';

interface HealthParameterEntry {
  id: string;
  type: HealthParameterType;
  value: number | { systolic: number; diastolic: number }; // BP is special
  unit: string;
  timestamp: Date;
  notes?: string;
  context?: string; // "before meal", "after exercise", etc.
}

interface Trend {
  direction: 'improving' | 'stable' | 'worsening';
  averageChange: number;
  dataPoints: number;
  recommendation: string;
}

interface HealthReport {
  period: { start: Date; end: Date };
  parameters: ParameterSummary[];
  adherenceRate: number;
  achievements: string[];
  concerns: string[];
  doctorNotes: string;
}

interface ParameterSummary {
  type: HealthParameterType;
  average: number;
  min: number;
  max: number;
  trend: Trend;
  inTargetRange: boolean;
  targetRange: { min: number; max: number };
}
```

#### 1.6 Reminder System
```typescript
interface ReminderSystem {
  scheduleReminders(prescription: PrescriptionData): Promise<void>;
  cancelReminders(prescriptionId: string): Promise<void>;
  snoozeReminder(reminderId: string, duration: number): Promise<void>;
  markAsTaken(reminderId: string): Promise<void>;
  getUpcomingReminders(): Promise<Reminder[]>;
}

interface Reminder {
  id: string;
  medicationName: string;
  dosage: string;
  scheduledTime: Date;
  status: 'pending' | 'taken' | 'missed' | 'snoozed';
  instructions: string;
  notificationId: string;
}
```

#### 1.7 Consent Management Module
```typescript
interface ConsentManager {
  grantConsent(consent: ConsentRequest): Promise<void>;
  revokeConsent(consentId: string): Promise<void>;
  getActiveConsents(): Promise<Consent[]>;
  updateConsentPreferences(consentId: string, preferences: ConsentPreferences): Promise<void>;
}

interface ConsentRequest {
  type: 'doctor_sharing' | 'research_participation' | 'emergency_access';
  recipientId: string;
  recipientName: string;
  dataTypes: DataType[];
  duration?: number; // days, null for indefinite
  purpose: string;
}

type DataType = 
  | 'prescriptions'
  | 'adherence'
  | 'health_parameters'
  | 'side_effects'
  | 'personal_notes';

interface Consent {
  id: string;
  type: ConsentRequest['type'];
  recipient: { id: string; name: string };
  dataTypes: DataType[];
  grantedDate: Date;
  expiryDate?: Date;
  status: 'active' | 'expired' | 'revoked';
  accessLog: AccessLogEntry[];
}

interface ConsentPreferences {
  dataTypes: DataType[];
  notifyOnAccess: boolean;
  autoExpiry: boolean;
}

interface AccessLogEntry {
  timestamp: Date;
  accessor: string;
  dataAccessed: DataType[];
  purpose: string;
}
```


### 2. Cloud Processing Services (AWS Lambda)

**Technology Stack:**
- Node.js 20.x runtime
- TypeScript
- AWS SDK v3
- Sharp for image processing
- PDF generation libraries

**Key Services:**

#### 2.1 Advanced OCR Service
```typescript
interface AdvancedOCRService {
  processImage(imageData: Buffer, options: OCROptions): Promise<AdvancedOCRResult>;
}

interface OCROptions {
  language: string;
  documentType: 'prescription' | 'lab_report' | 'medical_certificate';
  enhanceImage: boolean;
}

interface AdvancedOCRResult {
  text: string;
  confidence: number;
  structuredData: PrescriptionData;
  handwritingDetected: boolean;
  qualityScore: number;
  suggestions: string[];
}
```

#### 2.2 AI Explanation Service
```typescript
interface AIExplanationService {
  generateExplanation(
    prescription: PrescriptionData,
    patientContext: PatientContext,
    language: string
  ): Promise<AIExplanation>;
}

interface PatientContext {
  age?: number;
  gender?: string;
  chronicConditions?: string[];
  allergies?: string[];
  currentMedications?: string[];
  literacyLevel?: 'low' | 'medium' | 'high';
}

interface AIExplanation {
  conditionSummary: string;
  treatmentRationale: string;
  medicineExplanations: DetailedMedicineExplanation[];
  lifestyleRecommendations: string[];
  followUpGuidance: string;
  redFlags: string[];
}

interface DetailedMedicineExplanation {
  name: string;
  purpose: string;
  mechanism: string;
  expectedBenefits: string;
  timeToEffect: string;
  commonSideEffects: string[];
  whenToWorry: string[];
  interactionsWith: string[];
}
```

#### 2.3 Research Integration Service
```typescript
interface ResearchIntegrationService {
  searchRelevantPapers(query: ResearchQuery): Promise<ResearchPaper[]>;
  generateSummary(paperId: string): Promise<PaperSummary>;
  getEvidenceForTreatment(
    condition: string,
    treatment: string
  ): Promise<EvidenceBase>;
}

interface ResearchQuery {
  condition: string;
  treatment?: string;
  population?: string; // "Indian", "South Asian", etc.
  yearFrom?: number;
  studyTypes?: ('RCT' | 'meta-analysis' | 'cohort' | 'case-control')[];
}

interface ResearchPaper {
  id: string;
  title: string;
  authors: string[];
  journal: string;
  publicationDate: Date;
  doi: string;
  abstract: string;
  relevanceScore: number;
  studyType: string;
  sampleSize?: number;
  population?: string;
  keyFindings: string[];
}

interface PaperSummary {
  paperId: string;
  executiveSummary: string;
  methodology: string;
  keyResults: string[];
  clinicalImplications: string[];
  limitations: string[];
  evidenceLevel: 'A' | 'B' | 'C' | 'D';
}

interface EvidenceBase {
  treatment: string;
  condition: string;
  recommendationStrength: 'strong' | 'moderate' | 'weak';
  evidenceQuality: 'high' | 'moderate' | 'low' | 'very-low';
  supportingStudies: ResearchPaper[];
  guidelines: Guideline[];
  summary: string;
}

interface Guideline {
  organization: string; // "ICMR", "WHO", "ADA", etc.
  title: string;
  year: number;
  recommendation: string;
  url: string;
}
```

#### 2.4 Analytics Service
```typescript
interface AnalyticsService {
  aggregatePatientData(
    doctorId: string,
    filters: AnalyticsFilters
  ): Promise<CohortAnalytics>;
  generateRealWorldEvidence(
    condition: string,
    treatment: string
  ): Promise<RealWorldEvidence>;
  compareOutcomes(
    cohortId: string,
    benchmark: 'national' | 'research'
  ): Promise<OutcomeComparison>;
}

interface AnalyticsFilters {
  condition?: string;
  ageRange?: { min: number; max: number };
  gender?: string;
  dateRange: { start: Date; end: Date };
}

interface CohortAnalytics {
  totalPatients: number;
  demographics: Demographics;
  adherenceMetrics: AdherenceMetrics;
  outcomeMetrics: OutcomeMetrics;
  trends: TrendAnalysis[];
  alerts: PatientAlert[];
}

interface Demographics {
  ageDistribution: { [ageGroup: string]: number };
  genderDistribution: { male: number; female: number; other: number };
  geographicDistribution: { [region: string]: number };
}

interface AdherenceMetrics {
  overallRate: number;
  byMedication: { [medication: string]: number };
  byTimeOfDay: { morning: number; afternoon: number; evening: number; night: number };
  improvementOverTime: number;
}

interface OutcomeMetrics {
  patientsAtTarget: number;
  averageImprovement: number;
  complicationRate: number;
  hospitalizationRate: number;
  qualityOfLifeScore?: number;
}

interface TrendAnalysis {
  metric: string;
  direction: 'improving' | 'stable' | 'declining';
  changeRate: number;
  significance: 'significant' | 'not-significant';
}

interface PatientAlert {
  patientId: string;
  patientName: string;
  alertType: 'poor-adherence' | 'worsening-parameters' | 'missed-appointment' | 'side-effect';
  severity: 'low' | 'medium' | 'high';
  message: string;
  actionRequired: string;
}

interface RealWorldEvidence {
  condition: string;
  treatment: string;
  sampleSize: number;
  followUpDuration: number; // days
  effectivenessRate: number;
  adherenceRate: number;
  sideEffectRate: number;
  comparisonToBenchmark: {
    benchmark: string;
    ourRate: number;
    benchmarkRate: number;
    difference: number;
  };
  insights: string[];
}

interface OutcomeComparison {
  cohortMetrics: OutcomeMetrics;
  benchmarkMetrics: OutcomeMetrics;
  differences: { [metric: string]: number };
  interpretation: string;
  recommendations: string[];
}
```


### 3. Doctor Dashboard (Web Application)

**Technology Stack:**
- React 18 with TypeScript
- Next.js for SSR and routing
- TailwindCSS for styling
- Recharts for data visualization
- React Query for data fetching
- NextAuth.js for authentication

**Key Components:**

#### 3.1 Patient Monitoring Dashboard
```typescript
interface PatientMonitoringDashboard {
  getPatientList(filters: PatientFilters): Promise<PatientSummary[]>;
  getPatientDetails(patientId: string): Promise<DetailedPatientView>;
  getAlerts(): Promise<PatientAlert[]>;
}

interface PatientFilters {
  condition?: string;
  adherenceThreshold?: number;
  lastVisitDays?: number;
  sortBy: 'name' | 'lastVisit' | 'adherence' | 'risk';
}

interface PatientSummary {
  id: string;
  name: string;
  age: number;
  gender: string;
  primaryCondition: string;
  lastVisit: Date;
  nextVisit: Date;
  adherenceRate: number;
  riskLevel: 'low' | 'medium' | 'high';
  recentAlerts: number;
}

interface DetailedPatientView {
  patient: PatientSummary;
  currentMedications: MedicationStatus[];
  healthParameters: HealthParameterHistory[];
  adherenceTrend: AdherenceTrend;
  sideEffects: SideEffectReport[];
  progressSummary: string;
  recommendations: string[];
}

interface MedicationStatus {
  medication: string;
  dosage: string;
  prescribedDate: Date;
  adherenceRate: number;
  lastTaken: Date;
  missedDoses: number;
  status: 'active' | 'completed' | 'discontinued';
}

interface HealthParameterHistory {
  parameter: HealthParameterType;
  readings: HealthParameterEntry[];
  trend: Trend;
  inTargetRange: boolean;
  targetRange: { min: number; max: number };
}

interface AdherenceTrend {
  overall: number;
  byWeek: { week: string; rate: number }[];
  patterns: {
    bestDay: string;
    worstDay: string;
    bestTime: string;
    worstTime: string;
  };
}

interface SideEffectReport {
  medication: string;
  sideEffect: string;
  severity: 'mild' | 'moderate' | 'severe';
  reportedDate: Date;
  status: 'ongoing' | 'resolved';
  doctorNotified: boolean;
}
```

#### 3.2 Research Library Interface
```typescript
interface ResearchLibrary {
  searchPapers(query: string, filters: ResearchFilters): Promise<ResearchPaper[]>;
  getSavedPapers(): Promise<SavedPaper[]>;
  savePaper(paperId: string, notes?: string): Promise<void>;
  getRecommendations(specialty: string): Promise<ResearchPaper[]>;
}

interface ResearchFilters {
  yearFrom?: number;
  yearTo?: number;
  studyType?: string[];
  journal?: string[];
  population?: string;
  relevanceThreshold?: number;
}

interface SavedPaper extends ResearchPaper {
  savedDate: Date;
  notes: string;
  tags: string[];
  linkedPatients: string[]; // patient IDs where this research applies
}
```

#### 3.3 Clinical Decision Support Interface
```typescript
interface ClinicalDecisionSupport {
  getTreatmentRecommendations(
    diagnosis: string,
    patientProfile: PatientProfile
  ): Promise<TreatmentRecommendation[]>;
  checkPrescription(prescription: PrescriptionData): Promise<PrescriptionValidation>;
  getAlternatives(medication: string): Promise<MedicationAlternative[]>;
}

interface PatientProfile {
  age: number;
  gender: string;
  weight?: number;
  height?: number;
  chronicConditions: string[];
  allergies: string[];
  currentMedications: string[];
  labResults?: { [test: string]: number };
  kidneyFunction?: number; // eGFR
  liverFunction?: string;
}

interface TreatmentRecommendation {
  medication: string;
  dosage: string;
  frequency: string;
  duration: string;
  rationale: string;
  evidenceLevel: 'A' | 'B' | 'C' | 'D';
  guidelines: Guideline[];
  supportingStudies: ResearchPaper[];
  alternatives: MedicationAlternative[];
  considerations: string[];
}

interface PrescriptionValidation {
  isValid: boolean;
  warnings: ValidationWarning[];
  suggestions: string[];
  guidelineCompliance: {
    compliant: boolean;
    guideline: string;
    deviations: string[];
  };
}

interface ValidationWarning {
  type: 'interaction' | 'contraindication' | 'dosage' | 'duration' | 'guideline';
  severity: 'info' | 'warning' | 'critical';
  message: string;
  recommendation: string;
  source: string;
}

interface MedicationAlternative {
  medication: string;
  genericName: string;
  advantages: string[];
  disadvantages: string[];
  costComparison: number; // percentage difference
  evidenceComparison: string;
  switchingConsiderations: string[];
}
```


### 4. Government Data Integration Layer

**Technology Stack:**
- Python 3.11 for data scraping and ETL
- Apache Airflow for scheduled data updates
- AWS Glue for data transformation
- AWS Lambda for API integrations

**Key Components:**

#### 4.1 CDSCO Data Sync
```python
from typing import List, Dict, Optional
from datetime import datetime

class CDSCODataSync:
    """Syncs drug approval and safety data from CDSCO"""
    
    def fetch_approved_drugs(self) -> List[DrugApproval]:
        """Fetch list of CDSCO approved drugs"""
        pass
    
    def fetch_safety_alerts(self, since: datetime) -> List[SafetyAlert]:
        """Fetch drug safety alerts issued since given date"""
        pass
    
    def fetch_drug_recalls(self, since: datetime) -> List[DrugRecall]:
        """Fetch drug recalls since given date"""
        pass

class DrugApproval:
    drug_name: str
    generic_name: str
    manufacturer: str
    approval_date: datetime
    approval_number: str
    therapeutic_category: str
    prescription_required: bool
    schedule: Optional[str]  # H, H1, X, etc.

class SafetyAlert:
    alert_id: str
    drug_name: str
    alert_type: str
    severity: str
    description: str
    issued_date: datetime
    action_required: str
    affected_batches: Optional[List[str]]

class DrugRecall:
    recall_id: str
    drug_name: str
    manufacturer: str
    batch_numbers: List[str]
    reason: str
    recall_date: datetime
    risk_level: str
```

#### 4.2 ICMR Guidelines Sync
```python
class ICMRGuidelinesSync:
    """Syncs treatment guidelines from ICMR"""
    
    def fetch_guidelines(self, condition: Optional[str] = None) -> List[TreatmentGuideline]:
        """Fetch treatment guidelines for conditions"""
        pass
    
    def fetch_updates(self, since: datetime) -> List[GuidelineUpdate]:
        """Fetch guideline updates since given date"""
        pass

class TreatmentGuideline:
    guideline_id: str
    condition: str
    title: str
    version: str
    published_date: datetime
    last_updated: datetime
    organization: str  # "ICMR", "WHO", etc.
    summary: str
    first_line_treatment: List[TreatmentOption]
    second_line_treatment: List[TreatmentOption]
    special_populations: Dict[str, List[TreatmentOption]]
    monitoring_requirements: List[str]
    document_url: str

class TreatmentOption:
    medication: str
    dosage: str
    duration: str
    evidence_level: str
    considerations: List[str]
```

#### 4.3 NPPA Pricing Sync
```python
class NPPAPricingSync:
    """Syncs drug pricing data from NPPA"""
    
    def fetch_drug_prices(self) -> List[DrugPrice]:
        """Fetch official drug prices"""
        pass
    
    def fetch_price_updates(self, since: datetime) -> List[PriceUpdate]:
        """Fetch price changes since given date"""
        pass

class DrugPrice:
    drug_name: str
    generic_name: str
    manufacturer: str
    pack_size: str
    mrp: float
    ceiling_price: Optional[float]
    last_updated: datetime
    nlem_status: bool  # National List of Essential Medicines

class PriceUpdate:
    drug_name: str
    old_price: float
    new_price: float
    change_date: datetime
    reason: str
```

#### 4.4 Research Database Integration
```python
class ResearchDatabaseIntegration:
    """Integrates with PubMed, ICMR journals, etc."""
    
    def search_pubmed(self, query: str, filters: Dict) -> List[ResearchPaper]:
        """Search PubMed for relevant papers"""
        pass
    
    def fetch_paper_details(self, pmid: str) -> PaperDetails:
        """Fetch full details of a paper by PMID"""
        pass
    
    def get_citations(self, pmid: str) -> List[str]:
        """Get papers that cite this paper"""
        pass

class PaperDetails:
    pmid: str
    doi: Optional[str]
    title: str
    authors: List[str]
    journal: str
    publication_date: datetime
    abstract: str
    full_text_url: Optional[str]
    study_type: str
    mesh_terms: List[str]
    keywords: List[str]
```


## Data Models

### Core Data Entities

#### Patient Data Model
```typescript
interface Patient {
  id: string; // UUID
  userId: string; // Auth user ID
  profile: PatientProfile;
  preferences: PatientPreferences;
  healthRecords: HealthRecord[];
  prescriptions: Prescription[];
  consents: Consent[];
  createdAt: Date;
  updatedAt: Date;
}

interface PatientProfile {
  firstName: string;
  lastName: string;
  dateOfBirth: Date;
  gender: 'male' | 'female' | 'other';
  bloodGroup?: string;
  height?: number; // cm
  weight?: number; // kg
  chronicConditions: string[];
  allergies: string[];
  emergencyContacts: EmergencyContact[];
}

interface PatientPreferences {
  language: string;
  notificationsEnabled: boolean;
  reminderTimes: string[]; // preferred reminder times
  dataSharing: {
    allowDoctorSharing: boolean;
    allowResearchParticipation: boolean;
    allowAnonymizedAnalytics: boolean;
  };
  accessibility: {
    largeText: boolean;
    highContrast: boolean;
    voiceEnabled: boolean;
  };
}

interface EmergencyContact {
  name: string;
  relationship: string;
  phone: string;
  email?: string;
  canAccessHealthData: boolean;
}

interface HealthRecord {
  id: string;
  type: 'prescription' | 'lab_report' | 'diagnosis' | 'procedure';
  date: Date;
  provider: string;
  data: any; // type-specific data
  attachments: string[]; // S3 URLs
  sharedWith: string[]; // doctor IDs
}
```

#### Prescription Data Model
```typescript
interface Prescription {
  id: string;
  patientId: string;
  doctorId?: string;
  doctorName: string;
  date: Date;
  diagnosis: string;
  medications: PrescribedMedication[];
  instructions: string;
  followUpDate?: Date;
  status: 'active' | 'completed' | 'discontinued';
  scannedImageUrl?: string;
  extractedText?: string;
  ocrConfidence?: number;
  processingMode: 'local' | 'cloud';
  validatedAgainstGuidelines: boolean;
  guidelineReference?: string;
  createdAt: Date;
  updatedAt: Date;
}

interface PrescribedMedication {
  id: string;
  medicineId: string; // reference to Medicine table
  medicineName: string;
  genericName: string;
  dosage: string;
  frequency: string; // "1-0-1", "twice daily", etc.
  duration: string; // "30 days", "until finished", etc.
  instructions: string;
  withFood: boolean;
  startDate: Date;
  endDate?: Date;
  refillsRemaining?: number;
  adherenceTracking: AdherenceTracking;
}

interface AdherenceTracking {
  totalDoses: number;
  takenDoses: number;
  missedDoses: number;
  adherenceRate: number; // percentage
  lastTaken?: Date;
  streak: number; // consecutive days with 100% adherence
  reminders: Reminder[];
  logs: AdherenceLog[];
}

interface AdherenceLog {
  date: Date;
  time: string;
  status: 'taken' | 'missed' | 'skipped';
  notes?: string;
}
```

#### Medicine Data Model
```typescript
interface Medicine {
  id: string;
  name: string;
  genericName: string;
  manufacturer: string;
  activeIngredient: string;
  strength: string;
  form: 'tablet' | 'capsule' | 'syrup' | 'injection' | 'cream' | 'drops';
  mrp: number;
  ceilingPrice?: number;
  cdscoapproval: CDSCOApproval;
  nlemStatus: boolean;
  prescriptionRequired: boolean;
  schedule?: string; // H, H1, X, etc.
  category: string;
  therapeuticClass: string;
  pharmacologicalClass: string;
  indications: string[];
  contraindications: string[];
  sideEffects: SideEffect[];
  interactions: DrugInteraction[];
  dosageInformation: DosageInformation;
  storageConditions: string;
  pregnancyCategory?: string;
  lactationSafety?: string;
  pediatricUse?: string;
  geriatricConsiderations?: string;
  renalAdjustment?: string;
  hepaticAdjustment?: string;
  genericAlternatives: string[]; // medicine IDs
  brandedAlternatives: string[]; // medicine IDs
  lastUpdated: Date;
}

interface CDSCOApproval {
  approved: boolean;
  approvalNumber: string;
  approvalDate: Date;
  approvedIndications: string[];
}

interface SideEffect {
  effect: string;
  frequency: 'common' | 'uncommon' | 'rare' | 'very-rare';
  severity: 'mild' | 'moderate' | 'severe';
  description: string;
}

interface DrugInteraction {
  interactingDrug: string; // medicine ID or class
  severity: 'mild' | 'moderate' | 'severe';
  effect: string;
  mechanism: string;
  management: string;
  source: string;
}

interface DosageInformation {
  adult: DosageRange;
  pediatric?: DosageRange;
  geriatric?: DosageRange;
  maxDailyDose: string;
  administrationRoute: string[];
  timingRecommendations: string;
}

interface DosageRange {
  min: string;
  max: string;
  typical: string;
  unit: string;
}
```

#### Doctor Data Model
```typescript
interface Doctor {
  id: string;
  userId: string; // Auth user ID
  profile: DoctorProfile;
  credentials: Credentials;
  patients: string[]; // patient IDs with active consent
  specialties: string[];
  researchInterests: string[];
  savedPapers: SavedPaper[];
  preferences: DoctorPreferences;
  statistics: DoctorStatistics;
  createdAt: Date;
  updatedAt: Date;
}

interface DoctorProfile {
  firstName: string;
  lastName: string;
  title: string; // "Dr.", "Prof.", etc.
  qualifications: string[];
  registrationNumber: string;
  registrationCouncil: string; // "MCI", "State Medical Council", etc.
  hospitalAffiliation?: string;
  clinicAddress?: Address;
  phone: string;
  email: string;
}

interface Credentials {
  verified: boolean;
  verificationDate?: Date;
  verificationMethod: string;
  documents: string[]; // S3 URLs
}

interface Address {
  street: string;
  city: string;
  state: string;
  pincode: string;
  country: string;
}

interface DoctorPreferences {
  dashboardLayout: string;
  defaultFilters: any;
  notificationSettings: {
    patientAlerts: boolean;
    researchUpdates: boolean;
    guidelineUpdates: boolean;
  };
}

interface DoctorStatistics {
  totalPatients: number;
  activePatients: number;
  averageAdherenceRate: number;
  prescriptionsWritten: number;
  researchContributions: number;
  lastActive: Date;
}
```


#### Government Data Models
```typescript
interface TreatmentGuideline {
  id: string;
  condition: string;
  title: string;
  organization: string; // "ICMR", "WHO", "ADA", etc.
  version: string;
  publishedDate: Date;
  lastUpdated: Date;
  summary: string;
  recommendations: TreatmentRecommendation[];
  specialPopulations: SpecialPopulationGuidance[];
  monitoringProtocol: MonitoringRequirement[];
  documentUrl: string;
  references: string[];
}

interface TreatmentRecommendation {
  line: 'first' | 'second' | 'third';
  medications: string[];
  dosageGuidance: string;
  duration: string;
  evidenceLevel: 'A' | 'B' | 'C' | 'D';
  recommendationStrength: 'strong' | 'moderate' | 'weak';
  considerations: string[];
}

interface SpecialPopulationGuidance {
  population: 'pregnancy' | 'lactation' | 'pediatric' | 'geriatric' | 'renal-impairment' | 'hepatic-impairment';
  modifications: string[];
  contraindications: string[];
  monitoring: string[];
}

interface MonitoringRequirement {
  parameter: string;
  frequency: string;
  targetRange?: { min: number; max: number };
  action: string;
}

interface SafetyAlert {
  id: string;
  alertType: 'recall' | 'warning' | 'contraindication' | 'interaction';
  severity: 'critical' | 'high' | 'medium' | 'low';
  medicineIds: string[];
  title: string;
  description: string;
  issuedBy: string; // "CDSCO", "FDA", etc.
  issuedDate: Date;
  affectedBatches?: string[];
  actionRequired: string;
  patientGuidance: string;
  doctorGuidance: string;
  status: 'active' | 'resolved';
  resolvedDate?: Date;
}

interface DrugPricing {
  medicineId: string;
  medicineName: string;
  manufacturer: string;
  packSize: string;
  mrp: number;
  ceilingPrice?: number;
  janAushadhiPrice?: number;
  priceControlled: boolean;
  lastUpdated: Date;
  priceHistory: PriceHistoryEntry[];
}

interface PriceHistoryEntry {
  date: Date;
  price: number;
  changeReason?: string;
}
```

#### Analytics Data Models
```typescript
interface PatientCohort {
  id: string;
  doctorId: string;
  name: string;
  condition: string;
  patientIds: string[];
  createdDate: Date;
  metrics: CohortMetrics;
  benchmarks: Benchmark[];
}

interface CohortMetrics {
  totalPatients: number;
  demographics: Demographics;
  adherence: {
    overall: number;
    byMedication: { [medication: string]: number };
    trend: TrendData;
  };
  outcomes: {
    patientsAtTarget: number;
    averageImprovement: number;
    complicationRate: number;
    hospitalizationRate: number;
  };
  costEffectiveness: {
    averageMonthlyCost: number;
    genericAdoptionRate: number;
    totalSavings: number;
  };
}

interface Benchmark {
  source: string; // "national-average", "research-study", "guideline-target"
  metric: string;
  value: number;
  comparison: number; // difference from cohort
  interpretation: string;
}

interface TrendData {
  dataPoints: { date: Date; value: number }[];
  direction: 'improving' | 'stable' | 'declining';
  changeRate: number;
  significance: boolean;
}

interface RealWorldEvidenceStudy {
  id: string;
  title: string;
  condition: string;
  treatment: string;
  startDate: Date;
  endDate?: Date;
  status: 'recruiting' | 'active' | 'completed' | 'published';
  participatingDoctors: string[];
  totalPatients: number;
  inclusionCriteria: string[];
  exclusionCriteria: string[];
  primaryOutcome: string;
  secondaryOutcomes: string[];
  results?: StudyResults;
  publications: string[];
}

interface StudyResults {
  effectivenessRate: number;
  adherenceRate: number;
  sideEffectRate: number;
  dropoutRate: number;
  keyFindings: string[];
  limitations: string[];
  clinicalImplications: string[];
}
```


## Correctness Properties

A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.

### Property 1: OCR Accuracy for Printed Text
*For any* printed prescription image with clear text, when processed by the OCR engine, the extracted text accuracy SHALL be at least 85%.
**Validates: Requirements 1.1**

### Property 2: OCR Accuracy for Handwritten Text (Local Mode)
*For any* handwritten prescription image processed in Local_Mode, the OCR engine SHALL extract text with at least 60% accuracy.
**Validates: Requirements 1.2**

### Property 3: OCR Accuracy for Handwritten Text (Cloud Mode)
*For any* handwritten prescription image processed in Cloud_Mode, the OCR engine SHALL extract text with at least 90% accuracy.
**Validates: Requirements 1.3**

### Property 4: Prescription Field Extraction Completeness
*For any* prescription containing patient name, doctor name, date, diagnosis, medications, dosages, and instructions, the OCR engine SHALL successfully identify and structure all present fields.
**Validates: Requirements 1.4**

### Property 5: Low Confidence Notification
*For any* OCR extraction with confidence below 70%, the system SHALL notify the patient and suggest Cloud_Mode.
**Validates: Requirements 1.5**

### Property 6: Image Quality Guidance
*For any* prescription image with poor lighting or blur detected, the system SHALL provide guidance to retake the photo.
**Validates: Requirements 1.6**

### Property 7: Extraction Display
*For any* completed prescription processing, the system SHALL display the extracted text for patient verification.
**Validates: Requirements 1.7**

### Property 8: Local Mode Offline Operation
*For any* prescription processing in Local_Mode, the system SHALL complete all operations without requiring internet connectivity.
**Validates: Requirements 2.1, 2.2, 2.3, 2.4, 2.5, 2.6**

### Property 9: Cloud Data Deletion
*For any* data uploaded to cloud services for processing, the system SHALL delete the data from cloud servers immediately after processing completes.
**Validates: Requirements 2.8**

### Property 10: Medicine Database Completeness
*For any* medicine approved by CDSCO, the Medicine_Database SHALL contain its information.
**Validates: Requirements 3.1**

### Property 11: Medicine Information Retrieval
*For any* medicine in the database, when queried, the system SHALL return its purpose, mechanism, side effects, and precautions.
**Validates: Requirements 3.2**

### Property 12: Generic Alternative Display
*For any* branded medicine with a generic alternative, the system SHALL display the cost comparison between branded and generic versions.
**Validates: Requirements 3.4**

### Property 13: Drug Interaction Detection
*For any* set of multiple prescribed medicines, the system SHALL check for and display known drug interactions.
**Validates: Requirements 3.5**

### Property 14: Translation Accuracy
*For any* supported language, when a prescription is translated, the medical terminology SHALL be accurately converted while maintaining meaning.
**Validates: Requirements 4.2**

### Property 15: Voice Output Availability
*For any* prescription explanation, the system SHALL provide voice output in the patient's selected language.
**Validates: Requirements 4.5**

### Property 16: Simple Language Explanation
*For any* medical diagnosis, the system SHALL provide an explanation using language appropriate for 8th grade reading level.
**Validates: Requirements 5.2**

### Property 17: Medication Schedule Generation
*For any* processed prescription, the system SHALL generate a visual medication schedule showing timing for each medicine.
**Validates: Requirements 6.1**

### Property 18: Reminder Delivery
*For any* scheduled medication time with reminders enabled, the system SHALL send a notification at the scheduled time.
**Validates: Requirements 6.3**

### Property 19: Adherence Recording
*For any* medication dose marked as taken, the system SHALL record the adherence data with timestamp.
**Validates: Requirements 6.5**

### Property 20: CDSCO Approval Validation
*For any* prescription processed, the system SHALL validate that all prescribed medicines are CDSCO-approved.
**Validates: Requirements 7.4**

### Property 21: Guideline Comparison
*For any* prescription with a matching ICMR guideline, the system SHALL compare the treatment against the guideline.
**Validates: Requirements 7.5**

### Property 22: Safety Alert Notification
*For any* patient taking a medicine with a new safety alert, the system SHALL notify the patient within 24 hours.
**Validates: Requirements 7.8**

### Property 23: Generic Cost Savings Calculation
*For any* branded medicine with a generic alternative, the system SHALL accurately calculate and display potential monthly savings.
**Validates: Requirements 8.3**

### Property 24: Jan Aushadhi Location Display
*For any* medicine stocked at Jan_Aushadhi_Kendra pharmacies, the system SHALL show their locations and prices.
**Validates: Requirements 8.4**

### Property 25: Health Parameter Logging
*For any* health parameter entry, the system SHALL timestamp and store it in encrypted local storage.
**Validates: Requirements 9.2**

### Property 26: Trend Analysis
*For any* health parameter with multiple readings, the system SHALL display trend graphs showing changes over time.
**Validates: Requirements 9.3**

### Property 27: Out-of-Range Warning
*For any* health parameter reading outside normal range, the system SHALL highlight it with a warning indicator.
**Validates: Requirements 9.4**

### Property 28: Consent Requirement
*For any* data sharing with doctors, the system SHALL require explicit patient consent before transmission.
**Validates: Requirements 10.1**

### Property 29: Consent Revocation
*For any* revoked sharing permission, the system SHALL immediately stop data transmission and notify the doctor.
**Validates: Requirements 10.4**

### Property 30: Data Encryption in Transit
*For any* data shared with doctors, the system SHALL use end-to-end encryption during transmission.
**Validates: Requirements 10.6**

### Property 31: Adherence Alert Generation
*For any* patient whose adherence drops below 70%, the Doctor_Dashboard SHALL generate an alert.
**Validates: Requirements 11.4**

### Property 32: Parameter Trend Flagging
*For any* patient whose health parameters show concerning trends, the Doctor_Dashboard SHALL flag the patient for attention.
**Validates: Requirements 11.5**

### Property 33: Research Paper Relevance
*For any* doctor viewing a patient with a specific condition, the system SHALL suggest relevant recent research papers.
**Validates: Requirements 12.2**

### Property 34: Research Summary Generation
*For any* research paper, the system SHALL provide AI-generated summaries highlighting key findings.
**Validates: Requirements 12.3**

### Property 35: Treatment Recommendation Evidence
*For any* treatment recommendation, the system SHALL cite supporting guidelines and evidence level.
**Validates: Requirements 13.1, 13.7**

### Property 36: Drug Interaction Check
*For any* prescription with multiple medications, the system SHALL analyze for drug-drug interactions.
**Validates: Requirements 13.3**

### Property 37: Dosage Validation
*For any* prescription created, the system SHALL validate dosages against standard ranges for patient's age and weight.
**Validates: Requirements 13.4**

### Property 38: Data Anonymization
*For any* patient data collected for research, the system SHALL remove all personally identifiable information.
**Validates: Requirements 14.2**

### Property 39: Cohort Outcome Aggregation
*For any* patient cohort, the system SHALL accurately aggregate adherence rates, outcome metrics, and complication rates.
**Validates: Requirements 15.2**

### Property 40: Benchmark Comparison
*For any* cohort analysis, the system SHALL compare outcomes against national benchmarks from research literature.
**Validates: Requirements 15.3**

### Property 41: Prescription Safety Validation
*For any* prescription processed, the system SHALL check if dosages fall within safe ranges for the patient's age and weight.
**Validates: Requirements 16.2**

### Property 42: Interaction Warning Display
*For any* prescription containing medicines with known interactions, the system SHALL display interaction warnings with severity levels.
**Validates: Requirements 16.3**

### Property 43: Pregnancy Safety Check
*For any* prescription for a pregnant or breastfeeding patient, the system SHALL check pregnancy safety categories.
**Validates: Requirements 16.6**

### Property 44: Offline Core Functionality
*For any* core feature, when no internet connection is available, the system SHALL function fully in Local_Mode.
**Validates: Requirements 17.1**

### Property 45: Data Sync on Reconnection
*For any* queued data when internet connectivity is restored, the system SHALL automatically sync in the background.
**Validates: Requirements 17.3**

### Property 46: Data Encryption at Rest
*For any* patient data stored locally, the system SHALL encrypt it using AES-256 encryption.
**Validates: Requirements 18.1**

### Property 47: Data Encryption in Transit
*For any* data transmitted over network, the system SHALL encrypt it using TLS 1.3.
**Validates: Requirements 18.2**

### Property 48: Complete Data Deletion
*For any* patient requesting data deletion, the system SHALL remove all copies including backups within 24 hours.
**Validates: Requirements 18.8**

### Property 49: No Third-Party Data Sharing
*For any* patient data, the system SHALL never sell or share it with third parties for marketing purposes.
**Validates: Requirements 18.9**

### Property 50: Emergency Access Notification
*For any* emergency contact accessing patient data, the system SHALL notify the patient immediately.
**Validates: Requirements 19.3**

### Property 51: Personalization for Elderly
*For any* patient profile indicating elderly age, the system SHALL enable large text mode and voice features by default.
**Validates: Requirements 20.1**

### Property 52: Pregnancy Medicine Safety
*For any* patient profile indicating pregnancy, the system SHALL automatically check medicine safety for pregnancy.
**Validates: Requirements 20.2**

### Property 53: Data Export Format
*For any* patient requesting data export, the system SHALL generate a comprehensive health report in standard formats within 60 seconds.
**Validates: Requirements 21.2**

### Property 54: Adherence Pattern Detection
*For any* patient with 30 days of adherence data, the system SHALL identify patterns such as weekend non-compliance or evening dose misses.
**Validates: Requirements 22.2**

### Property 55: Side Effect Severity Assessment
*For any* reported side effect, the system SHALL check if it is a known common side effect and assess severity.
**Validates: Requirements 23.2, 23.3**

### Property 56: Doctor Notification on Side Effects
*For any* patient reporting side effects, the system SHALL notify the prescribing doctor.
**Validates: Requirements 23.5**

### Property 57: Treatment Outcome Comparison
*For any* completed treatment, the system SHALL compare pre-treatment and post-treatment health parameters to measure improvement.
**Validates: Requirements 24.3**

### Property 58: Educational Content Recommendation
*For any* patient diagnosed with a condition, the system SHALL recommend relevant educational content from trusted sources.
**Validates: Requirements 25.2**


## Error Handling

### Error Categories and Handling Strategies

#### 1. OCR Processing Errors
```typescript
enum OCRErrorType {
  IMAGE_TOO_BLURRY = 'IMAGE_TOO_BLURRY',
  POOR_LIGHTING = 'POOR_LIGHTING',
  TEXT_NOT_DETECTED = 'TEXT_NOT_DETECTED',
  LOW_CONFIDENCE = 'LOW_CONFIDENCE',
  UNSUPPORTED_FORMAT = 'UNSUPPORTED_FORMAT',
  PROCESSING_TIMEOUT = 'PROCESSING_TIMEOUT'
}

interface OCRError {
  type: OCRErrorType;
  message: string;
  userGuidance: string;
  retryable: boolean;
  suggestCloudMode: boolean;
}
```

**Handling Strategy:**
- Provide clear, actionable guidance to users
- Suggest retaking photo with better lighting/focus
- Offer Cloud_Mode as fallback for difficult cases
- Never fail silently - always inform user of issues
- Log errors for quality improvement

#### 2. Network and Connectivity Errors
```typescript
enum NetworkErrorType {
  NO_INTERNET = 'NO_INTERNET',
  TIMEOUT = 'TIMEOUT',
  SERVER_ERROR = 'SERVER_ERROR',
  RATE_LIMIT = 'RATE_LIMIT',
  AUTHENTICATION_FAILED = 'AUTHENTICATION_FAILED'
}

interface NetworkError {
  type: NetworkErrorType;
  message: string;
  retryable: boolean;
  fallbackToLocal: boolean;
  queueForLater: boolean;
}
```

**Handling Strategy:**
- Gracefully degrade to Local_Mode when cloud unavailable
- Queue operations for later sync when appropriate
- Implement exponential backoff for retries
- Cache responses to reduce network dependency
- Provide offline-first experience

#### 3. Data Validation Errors
```typescript
enum ValidationErrorType {
  INVALID_DOSAGE = 'INVALID_DOSAGE',
  DRUG_INTERACTION = 'DRUG_INTERACTION',
  CONTRAINDICATION = 'CONTRAINDICATION',
  MISSING_REQUIRED_FIELD = 'MISSING_REQUIRED_FIELD',
  INVALID_DATE = 'INVALID_DATE',
  DUPLICATE_ENTRY = 'DUPLICATE_ENTRY'
}

interface ValidationError {
  type: ValidationErrorType;
  field: string;
  message: string;
  severity: 'error' | 'warning' | 'info';
  suggestion: string;
}
```

**Handling Strategy:**
- Validate data at multiple layers (client, API, database)
- Provide specific, helpful error messages
- Suggest corrections when possible
- Allow users to override warnings with confirmation
- Log validation failures for pattern analysis

#### 4. Database and Storage Errors
```typescript
enum StorageErrorType {
  DISK_FULL = 'DISK_FULL',
  PERMISSION_DENIED = 'PERMISSION_DENIED',
  CORRUPTION = 'CORRUPTION',
  SYNC_CONFLICT = 'SYNC_CONFLICT',
  ENCRYPTION_FAILED = 'ENCRYPTION_FAILED'
}

interface StorageError {
  type: StorageErrorType;
  message: string;
  recoverable: boolean;
  dataLost: boolean;
  action: string;
}
```

**Handling Strategy:**
- Implement automatic backup before critical operations
- Use transactions to ensure data consistency
- Provide conflict resolution for sync issues
- Alert users to storage issues early
- Maintain data integrity at all costs

#### 5. Authentication and Authorization Errors
```typescript
enum AuthErrorType {
  INVALID_CREDENTIALS = 'INVALID_CREDENTIALS',
  SESSION_EXPIRED = 'SESSION_EXPIRED',
  INSUFFICIENT_PERMISSIONS = 'INSUFFICIENT_PERMISSIONS',
  ACCOUNT_LOCKED = 'ACCOUNT_LOCKED',
  MFA_REQUIRED = 'MFA_REQUIRED'
}

interface AuthError {
  type: AuthErrorType;
  message: string;
  action: 'login' | 'refresh' | 'contact-support';
  redirectUrl?: string;
}
```

**Handling Strategy:**
- Implement secure session management
- Provide clear authentication flows
- Support biometric authentication for convenience
- Lock accounts after failed attempts
- Maintain audit logs for security

#### 6. External API Errors
```typescript
enum ExternalAPIErrorType {
  CDSCO_UNAVAILABLE = 'CDSCO_UNAVAILABLE',
  ICMR_UNAVAILABLE = 'ICMR_UNAVAILABLE',
  RESEARCH_API_ERROR = 'RESEARCH_API_ERROR',
  RATE_LIMIT_EXCEEDED = 'RATE_LIMIT_EXCEEDED',
  INVALID_RESPONSE = 'INVALID_RESPONSE'
}

interface ExternalAPIError {
  type: ExternalAPIErrorType;
  service: string;
  message: string;
  useCachedData: boolean;
  retryAfter?: number;
}
```

**Handling Strategy:**
- Cache government data for offline access
- Implement circuit breakers for failing services
- Provide degraded functionality when APIs unavailable
- Use stale data with warnings when necessary
- Monitor API health and alert on issues

### Global Error Handling Principles

1. **User-Friendly Messages**: Never show technical stack traces to users
2. **Actionable Guidance**: Always tell users what they can do next
3. **Graceful Degradation**: System should remain functional even when components fail
4. **Error Logging**: Log all errors for debugging and improvement
5. **Privacy Protection**: Never log sensitive patient data in error logs
6. **Recovery Mechanisms**: Provide automatic recovery where possible
7. **User Notification**: Inform users of errors that affect their experience
8. **Fallback Options**: Always provide alternative paths when primary fails


## Testing Strategy

### Dual Testing Approach

The MedTrust platform requires comprehensive testing using both unit tests and property-based tests. These approaches are complementary:

- **Unit Tests**: Verify specific examples, edge cases, and error conditions
- **Property Tests**: Verify universal properties across all inputs

Together, they provide comprehensive coverage where unit tests catch concrete bugs and property tests verify general correctness.

### Property-Based Testing Framework

**Technology**: fast-check (JavaScript/TypeScript property-based testing library)

**Configuration**:
- Minimum 100 iterations per property test (due to randomization)
- Each property test must reference its design document property
- Tag format: `Feature: medtrust-healthcare-platform, Property {number}: {property_text}`

**Example Property Test Structure**:
```typescript
import fc from 'fast-check';

describe('Feature: medtrust-healthcare-platform', () => {
  it('Property 1: OCR Accuracy for Printed Text', () => {
    // Feature: medtrust-healthcare-platform, Property 1: OCR Accuracy for Printed Text
    fc.assert(
      fc.property(
        fc.prescriptionImage({ type: 'printed', quality: 'clear' }),
        async (prescriptionImage) => {
          const result = await ocrEngine.extractText(prescriptionImage);
          const accuracy = calculateAccuracy(result.text, prescriptionImage.groundTruth);
          expect(accuracy).toBeGreaterThanOrEqual(0.85);
        }
      ),
      { numRuns: 100 }
    );
  });
});
```

### Testing Layers

#### 1. Unit Testing

**Scope**: Individual functions, components, and modules

**Test Cases**:
- Valid input handling
- Invalid input rejection
- Edge cases (empty strings, null values, boundary conditions)
- Error conditions
- State transitions
- UI component rendering
- API response handling

**Tools**:
- Jest for test runner
- React Testing Library for UI components
- Supertest for API testing
- Mock Service Worker for API mocking

**Example Unit Tests**:
```typescript
describe('Medicine Database', () => {
  it('should return medicine information for valid medicine ID', async () => {
    const medicine = await medicineDB.getMedicineById('MED001');
    expect(medicine).toBeDefined();
    expect(medicine.name).toBe('Metformin');
  });

  it('should throw error for invalid medicine ID', async () => {
    await expect(medicineDB.getMedicineById('INVALID')).rejects.toThrow();
  });

  it('should handle empty search query', async () => {
    const results = await medicineDB.searchMedicine('');
    expect(results).toEqual([]);
  });
});
```

#### 2. Property-Based Testing

**Scope**: Universal properties that should hold for all inputs

**Key Properties to Test**:

**OCR Properties**:
- Property 1-7: OCR accuracy, field extraction, notifications

**Local Mode Properties**:
- Property 8: Offline operation without internet
- Property 44: Core functionality works offline

**Data Privacy Properties**:
- Property 9: Cloud data deletion after processing
- Property 28: Consent required before sharing
- Property 46-49: Encryption and privacy guarantees

**Medicine Database Properties**:
- Property 10-13: Database completeness and retrieval

**Translation Properties**:
- Property 14-15: Translation accuracy and voice output

**Reminder Properties**:
- Property 17-19: Schedule generation, delivery, recording

**Validation Properties**:
- Property 20-22: CDSCO validation, guideline comparison, alerts
- Property 41-43: Safety validation, interactions, pregnancy checks

**Cost Properties**:
- Property 23-24: Cost calculations and savings

**Health Tracking Properties**:
- Property 25-27: Logging, trends, warnings

**Consent Properties**:
- Property 28-30: Consent management and encryption

**Doctor Dashboard Properties**:
- Property 31-32: Alert generation and flagging

**Research Properties**:
- Property 33-35: Paper relevance and recommendations

**Clinical Decision Support Properties**:
- Property 36-37: Interaction checks and dosage validation

**Analytics Properties**:
- Property 38-40: Anonymization, aggregation, benchmarking

**Personalization Properties**:
- Property 51-52: Adaptive UI for different patient types

**Example Property Tests**:
```typescript
describe('Property-Based Tests', () => {
  it('Property 8: Local Mode Offline Operation', () => {
    // Feature: medtrust-healthcare-platform, Property 8
    fc.assert(
      fc.property(
        fc.prescriptionData(),
        async (prescription) => {
          // Disable network
          await networkSimulator.goOffline();
          
          // Process prescription in local mode
          const result = await system.processPrescription(prescription, 'local');
          
          // Verify processing completed without network calls
          expect(result.success).toBe(true);
          expect(networkSimulator.getRequestCount()).toBe(0);
          
          // Re-enable network
          await networkSimulator.goOnline();
        }
      ),
      { numRuns: 100 }
    );
  });

  it('Property 9: Cloud Data Deletion', () => {
    // Feature: medtrust-healthcare-platform, Property 9
    fc.assert(
      fc.property(
        fc.prescriptionImage(),
        async (image) => {
          // Upload to cloud
          const uploadResult = await cloudService.upload(image);
          const imageId = uploadResult.id;
          
          // Process image
          await cloudService.processImage(imageId);
          
          // Verify image is deleted
          await expect(cloudService.getImage(imageId)).rejects.toThrow('Not Found');
        }
      ),
      { numRuns: 100 }
    );
  });

  it('Property 12: Generic Alternative Display', () => {
    // Feature: medtrust-healthcare-platform, Property 12
    fc.assert(
      fc.property(
        fc.brandedMedicineWithGeneric(),
        async (medicine) => {
          const result = await system.getMedicineInfo(medicine.id);
          
          // Verify generic alternative is shown
          expect(result.genericAlternatives).toBeDefined();
          expect(result.genericAlternatives.length).toBeGreaterThan(0);
          
          // Verify cost comparison is present
          expect(result.costAnalysis).toBeDefined();
          expect(result.costAnalysis.brandedCost).toBeGreaterThan(0);
          expect(result.costAnalysis.genericCost).toBeGreaterThan(0);
          expect(result.costAnalysis.savings).toBeGreaterThan(0);
        }
      ),
      { numRuns: 100 }
    );
  });

  it('Property 28: Consent Requirement', () => {
    // Feature: medtrust-healthcare-platform, Property 28
    fc.assert(
      fc.property(
        fc.patientData(),
        fc.doctorId(),
        async (patientData, doctorId) => {
          // Attempt to share without consent
          await expect(
            system.shareDataWithDoctor(patientData.id, doctorId)
          ).rejects.toThrow('Consent required');
          
          // Grant consent
          await system.grantConsent(patientData.id, doctorId, ['prescriptions']);
          
          // Now sharing should work
          const result = await system.shareDataWithDoctor(patientData.id, doctorId);
          expect(result.success).toBe(true);
        }
      ),
      { numRuns: 100 }
    );
  });

  it('Property 46: Data Encryption at Rest', () => {
    // Feature: medtrust-healthcare-platform, Property 46
    fc.assert(
      fc.property(
        fc.patientData(),
        async (patientData) => {
          // Store data
          await storage.save(patientData);
          
          // Read raw storage (bypassing app layer)
          const rawData = await storage.readRaw(patientData.id);
          
          // Verify data is encrypted (not readable as plain text)
          expect(rawData).not.toContain(patientData.name);
          expect(rawData).not.toContain(patientData.diagnosis);
          
          // Verify we can decrypt through proper channels
          const decrypted = await storage.load(patientData.id);
          expect(decrypted.name).toBe(patientData.name);
        }
      ),
      { numRuns: 100 }
    );
  });
});
```

#### 3. Integration Testing

**Scope**: Multiple components working together

**Test Scenarios**:
- End-to-end prescription scanning flow
- Patient-doctor data sharing flow
- Offline-to-online sync
- Government data integration
- Research paper search and retrieval
- Clinical decision support workflow

**Tools**:
- Cypress for E2E testing
- Testcontainers for database testing
- AWS LocalStack for AWS service mocking

#### 4. Performance Testing

**Scope**: System performance under load

**Metrics**:
- OCR processing time < 5 seconds (local mode)
- API response time < 500ms (p95)
- Database query time < 100ms (p95)
- Mobile app startup time < 2 seconds
- Concurrent user capacity: 1M users

**Tools**:
- k6 for load testing
- Lighthouse for mobile performance
- AWS CloudWatch for monitoring

#### 5. Security Testing

**Scope**: Security vulnerabilities and compliance

**Tests**:
- Penetration testing
- Encryption verification
- Authentication bypass attempts
- SQL injection prevention
- XSS prevention
- CSRF protection
- Data leakage prevention

**Tools**:
- OWASP ZAP for security scanning
- Snyk for dependency vulnerabilities
- AWS Security Hub for cloud security

#### 6. Accessibility Testing

**Scope**: WCAG 2.1 Level AA compliance

**Tests**:
- Screen reader compatibility
- Keyboard navigation
- Color contrast ratios
- Text scaling
- Voice command functionality

**Tools**:
- axe DevTools
- NVDA screen reader
- VoiceOver (iOS)

### Test Data Generation

**Custom Generators for Property Tests**:
```typescript
// Custom arbitraries for domain-specific data
const prescriptionArbitrary = fc.record({
  patientName: fc.fullName(),
  doctorName: fc.fullName(),
  date: fc.date(),
  diagnosis: fc.constantFrom('Type 2 Diabetes', 'Hypertension', 'Asthma'),
  medications: fc.array(medicationArbitrary, { minLength: 1, maxLength: 5 })
});

const medicationArbitrary = fc.record({
  name: fc.constantFrom('Metformin', 'Amlodipine', 'Salbutamol'),
  dosage: fc.constantFrom('500mg', '5mg', '100mcg'),
  frequency: fc.constantFrom('1-0-1', '0-0-1', '1-1-1'),
  duration: fc.constantFrom('30 days', '90 days', 'ongoing')
});

const prescriptionImageArbitrary = fc.record({
  imageData: fc.uint8Array({ minLength: 1000, maxLength: 5000 }),
  type: fc.constantFrom('printed', 'handwritten'),
  quality: fc.constantFrom('clear', 'blurry', 'poor-lighting'),
  groundTruth: fc.string() // known correct text
});
```

### Continuous Integration

**CI/CD Pipeline**:
1. Code commit triggers pipeline
2. Run linting and type checking
3. Run unit tests (fast feedback)
4. Run property-based tests (100 iterations)
5. Run integration tests
6. Run security scans
7. Build and deploy to staging
8. Run E2E tests on staging
9. Deploy to production (if all pass)

**Test Coverage Goals**:
- Unit test coverage: > 80%
- Property test coverage: All critical properties
- Integration test coverage: All user flows
- E2E test coverage: All happy paths + critical error paths

### Testing Best Practices

1. **Test Isolation**: Each test should be independent
2. **Fast Feedback**: Unit tests run in < 1 minute
3. **Deterministic**: Tests should not be flaky
4. **Meaningful**: Tests should verify actual requirements
5. **Maintainable**: Tests should be easy to update
6. **Documented**: Complex tests should have comments
7. **Realistic Data**: Use realistic test data, not just "test123"
8. **Error Cases**: Test error handling, not just happy paths

