# MedTrust Project Structure

This document describes the organization of the MedTrust codebase.

## Directory Structure

```
MedTrust/
├── .github/                    # GitHub configuration
│   ├── ISSUE_TEMPLATE/        # Issue templates
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── workflows/             # GitHub Actions CI/CD
│   │   └── ci.yml
│   └── pull_request_template.md
│
├── .kiro/                     # Kiro AI specifications
│   └── specs/
│       └── medtrust-healthcare-platform/
│           ├── requirements.md
│           ├── design.md
│           ├── tasks.md
│           └── presentation files
│
├── docs/                      # Documentation
│   ├── requirements.md        # Requirements specification
│   ├── design.md             # Architecture & design
│   ├── tasks.md              # Implementation tasks
│   ├── PROJECT_STRUCTURE.md  # This file
│   └── assets/               # Images, diagrams
│
├── src/                       # Source code (to be created)
│   ├── components/           # React Native components
│   │   ├── common/          # Reusable components
│   │   ├── prescription/    # Prescription-related components
│   │   ├── health/          # Health tracking components
│   │   └── doctor/          # Doctor dashboard components
│   │
│   ├── screens/              # App screens
│   │   ├── auth/            # Authentication screens
│   │   ├── home/            # Home screen
│   │   ├── scanner/         # Prescription scanner
│   │   ├── medications/     # Medication management
│   │   ├── health/          # Health tracking
│   │   └── settings/        # Settings
│   │
│   ├── services/             # Business logic
│   │   ├── ocr/             # OCR service
│   │   ├── database/        # Database service
│   │   ├── api/             # API clients
│   │   ├── auth/            # Authentication
│   │   └── sync/            # Data synchronization
│   │
│   ├── store/                # Redux store
│   │   ├── slices/          # Redux slices
│   │   ├── actions/         # Actions
│   │   └── selectors/       # Selectors
│   │
│   ├── utils/                # Utility functions
│   │   ├── encryption/      # Encryption utilities
│   │   ├── validation/      # Validation functions
│   │   ├── formatting/      # Formatting utilities
│   │   └── helpers/         # General helpers
│   │
│   ├── types/                # TypeScript types
│   │   ├── models/          # Data models
│   │   ├── api/             # API types
│   │   └── common/          # Common types
│   │
│   ├── constants/            # Constants
│   │   ├── config.ts        # Configuration
│   │   ├── colors.ts        # Color palette
│   │   └── strings.ts       # String constants
│   │
│   ├── hooks/                # Custom React hooks
│   ├── navigation/           # Navigation configuration
│   ├── i18n/                 # Internationalization
│   │   ├── locales/         # Translation files
│   │   └── config.ts        # i18n configuration
│   │
│   └── assets/               # Static assets
│       ├── images/          # Images
│       ├── icons/           # Icons
│       └── fonts/           # Fonts
│
├── tests/                     # Test files
│   ├── unit/                 # Unit tests
│   ├── integration/          # Integration tests
│   ├── property/             # Property-based tests
│   ├── e2e/                  # End-to-end tests
│   └── fixtures/             # Test fixtures
│
├── aws/                       # AWS infrastructure (to be created)
│   ├── lambda/               # Lambda functions
│   │   ├── ocr/             # OCR service
│   │   ├── ai/              # AI explanation service
│   │   └── analytics/       # Analytics service
│   │
│   ├── cdk/                  # AWS CDK infrastructure
│   │   ├── lib/             # CDK stacks
│   │   └── bin/             # CDK app
│   │
│   └── scripts/              # Deployment scripts
│
├── android/                   # Android native code
├── ios/                       # iOS native code
│
├── .env.example              # Environment variables template
├── .gitignore               # Git ignore rules
├── .eslintrc.js             # ESLint configuration
├── .prettierrc              # Prettier configuration
├── tsconfig.json            # TypeScript configuration
├── jest.config.js           # Jest configuration
├── package.json             # NPM dependencies
├── README.md                # Project README
├── CONTRIBUTING.md          # Contributing guidelines
├── CODE_OF_CONDUCT.md       # Code of conduct
├── LICENSE                  # MIT License
├── SECURITY.md              # Security policy
└── CHANGELOG.md             # Changelog

```

## Key Directories

### `/src`
Contains all application source code. Organized by feature and responsibility.

### `/docs`
Comprehensive documentation including requirements, design, and guides.

### `/tests`
All test files organized by test type (unit, integration, property-based, e2e).

### `/aws`
AWS infrastructure code including Lambda functions and CDK definitions.

### `/.github`
GitHub-specific files including issue templates, PR templates, and CI/CD workflows.

### `/.kiro`
Kiro AI specifications and planning documents.

## File Naming Conventions

- **Components**: PascalCase (e.g., `PrescriptionScanner.tsx`)
- **Utilities**: camelCase (e.g., `formatDate.ts`)
- **Constants**: UPPER_SNAKE_CASE (e.g., `API_BASE_URL`)
- **Types**: PascalCase with `.types.ts` suffix (e.g., `Prescription.types.ts`)
- **Tests**: Same as source file with `.test.ts` suffix (e.g., `PrescriptionScanner.test.tsx`)
- **Property Tests**: `.property.test.ts` suffix (e.g., `ocr.property.test.ts`)

## Import Organization

Imports should be organized in the following order:

1. External libraries (React, React Native, etc.)
2. Internal modules (services, utils, etc.)
3. Types
4. Constants
5. Styles

Example:
```typescript
// External
import React, { useState } from 'react';
import { View, Text } from 'react-native';

// Internal
import { ocrService } from '@/services/ocr';
import { formatDate } from '@/utils/formatting';

// Types
import type { Prescription } from '@/types/models';

// Constants
import { COLORS } from '@/constants/colors';

// Styles
import styles from './PrescriptionScanner.styles';
```

## Module Aliases

The following module aliases are configured:

- `@/` → `src/`
- `@components/` → `src/components/`
- `@screens/` → `src/screens/`
- `@services/` → `src/services/`
- `@utils/` → `src/utils/`
- `@types/` → `src/types/`
- `@constants/` → `src/constants/`
- `@hooks/` → `src/hooks/`
- `@assets/` → `src/assets/`

## Code Organization Principles

1. **Feature-based organization**: Group related files by feature
2. **Separation of concerns**: Keep business logic separate from UI
3. **Reusability**: Create reusable components and utilities
4. **Type safety**: Use TypeScript for all code
5. **Testability**: Write testable code with clear dependencies
6. **Documentation**: Document complex logic and public APIs

## Adding New Features

When adding a new feature:

1. Create feature directory in appropriate location
2. Add types in `/src/types`
3. Create components in `/src/components`
4. Add business logic in `/src/services`
5. Write tests in `/tests`
6. Update documentation in `/docs`
7. Add to CHANGELOG.md

## Best Practices

- Keep files small and focused (< 300 lines)
- Use meaningful names for files and variables
- Write self-documenting code
- Add comments for complex logic
- Follow the established patterns
- Write tests for new code
- Update documentation

---

For more information, see:
- [Contributing Guide](../CONTRIBUTING.md)
- [Architecture Design](design.md)
- [Implementation Tasks](tasks.md)
