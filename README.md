# Travel Visitation Agency - Project Explanation

## Table of Contents
1. [Project Overview](#project-overview)
2. [Frontend Entry Point](#frontend-entry-point)
3. [Exam Requirements Implementation](#exam-requirements-implementation)
4. [Code Reusability](#code-reusability)
5. [Architecture Overview](#architecture-overview)

---

## Project Overview

This is a full-stack web application for a Travel Visitation Agency that allows users to book tours around Rwanda. The system consists of:

- **Frontend**: React.js application (`travelvisitationagencyfe/`)
- **Backend**: Spring Boot REST API (`TravelVisitationAgency/`)
- **Database**: PostgreSQL

The application supports two main user roles:
- **ADMIN**: Manages all system entities (users, locations, destinations, packages, bookings, payments)
- **TOURIST**: Views destinations/packages, makes bookings, and manages their own bookings/payments

---

## Frontend Entry Point

**The frontend application runs from `index.js`, not `App.js`.**

### Entry Point Flow:
1. **`travelvisitationagencyfe/src/index.js`** (Entry Point)
   - Creates the React root using `ReactDOM.createRoot()`
   - Wraps the application in `BrowserRouter` for routing
   - Renders the `App` component

2. **`travelvisitationagencyfe/src/App.js`** (Main App Component)
   - Simple wrapper that renders `AppRoutes`
   - Acts as the root component for the application

3. **`travelvisitationagencyfe/src/routes/AppRoutes.jsx`** (Routing Configuration)
   - Defines all application routes
   - Handles authentication and role-based route protection
   - Implements the root redirect to `/login`

### Key Files:
- **Entry**: `travelvisitationagencyfe/src/index.js`
- **App Component**: `travelvisitationagencyfe/src/App.js`
- **Routes**: `travelvisitationagencyfe/src/routes/AppRoutes.jsx`
- **Protected Routes**: `travelvisitationagencyfe/src/routes/ProtectedRoute.jsx`
- **Layout**: `travelvisitationagencyfe/src/components/layout/AppLayout.jsx`

---

## Exam Requirements Implementation

### 1. The system should have at least 5 entities. (4 pts)

**Status**: ✅ **IMPLEMENTED** - The system has **6 main entities** plus supporting entities.

#### Main Entities:

1. **User** (`TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/User.java`)
   - Fields: `id`, `fullName`, `email`, `phone`, `passwordHash`, `username`, `role`, `location`, `isActive`, `createdAt`, `twoFactorEnabled`, `twoFactorEmail`
   - Relationships: Many-to-One with `Location`
   - Table: `system_users`

2. **Location** (`TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/Location.java`)
   - Fields: `id`, `name`, `code`, `type`, `parent`
   - Relationships: Self-referential (parent-child for hierarchical locations)
   - Table: `locations`
   - Types: PROVINCE, DISTRICT, SECTOR, CELL, VILLAGE

3. **Destination** (`TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/Destination.java`)
   - Fields: `id`, `name`, `category`, `summary`, `ratingAvg`
   - Relationships: Many-to-Many with `TourPackage`
   - Table: `destinations`

4. **TourPackage** (`TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/TourPackage.java`)
   - Fields: `id`, `title`, `description`, `basePricePerPerson`, `durationDays`, `maxPeople`, `destinations`, `createdByUser`
   - Relationships: Many-to-Many with `Destination`, Many-to-One with `User`
   - Table: `tour_packages`

5. **Booking** (`TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/Booking.java`)
   - Fields: `id`, `user`, `tourPackage`, `peopleCount`, `startDate`, `endDate`, `status`
   - Relationships: Many-to-One with `User` and `TourPackage`
   - Table: `bookings`
   - Status: PENDING, CONFIRMED, CANCELLED

6. **Payment** (`TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/Payment.java`)
   - Fields: `id`, `booking`, `amount`, `reference`, `status`, `paidAt`
   - Relationships: Many-to-One with `Booking`
   - Table: `payments`
   - Status: PENDING, PAID, FAILED

#### Supporting Entities (for authentication and security):
- `AuthToken` - Stores authentication tokens
- `TwoFactorCode` - Stores 2FA verification codes
- `PasswordResetCode` - Stores password reset codes
- `PasswordResetToken` - Stores password reset tokens

**Implementation Files**:
- All entity models: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/`
- Repositories: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/repository/`
- Services: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/`
- Controllers: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/`

---

### 2. The system should have at least 5 pages, excluding the login, forget password, and sign-up pages. (5 pts)

**Status**: ✅ **IMPLEMENTED** - The system has **9 main pages** (excluding auth pages).

#### Main Pages:

1. **Dashboard** (`travelvisitationagencyfe/src/pages/Dashboard.jsx`)
   - Route: `/dashboard`
   - Shows business information summary (stats cards)
   - Different views for ADMIN vs TOURIST roles
   - ADMIN: Total users, destinations, packages, bookings, payments
   - TOURIST: Available destinations, packages, my bookings, pending payments

2. **Destinations** (`travelvisitationagencyfe/src/pages/Destinations.jsx`)
   - Route: `/destinations`
   - View all destinations
   - Create/edit destinations (ADMIN only)
   - Search and pagination

3. **Locations** (`travelvisitationagencyfe/src/pages/Locations.jsx`)
   - Route: `/locations`
   - ADMIN only
   - Hierarchical location management (Provinces → Districts → Sectors → Cells → Villages)
   - Create/edit locations
   - Search and pagination

4. **Packages** (`travelvisitationagencyfe/src/pages/Packages.jsx`)
   - Route: `/packages`
   - View all tour packages
   - Create/edit packages (ADMIN only)
   - Book packages (TOURIST)
   - Search and pagination

5. **Bookings** (`travelvisitationagencyfe/src/pages/Bookings.jsx`)
   - Route: `/bookings`
   - ADMIN: View all bookings
   - TOURIST: View own bookings ("My Bookings")
   - Create/edit bookings
   - Cancel bookings
   - Search and pagination

6. **Payments** (`travelvisitationagencyfe/src/pages/Payments.jsx`)
   - Route: `/payments`
   - ADMIN: View all payments
   - TOURIST: View own payments
   - Generate invoices (PDF)
   - Search and pagination

7. **Users** (`travelvisitationagencyfe/src/pages/Users.jsx`)
   - Route: `/users`
   - ADMIN only
   - View all users
   - Create/edit users
   - Search and pagination

8. **Profile** (`travelvisitationagencyfe/src/pages/Profile.jsx`)
   - Route: `/profile`
   - View and edit own profile
   - Change password
   - Enable/disable 2FA

9. **Search** (`travelvisitationagencyfe/src/pages/Search.jsx`)
   - Route: `/search`
   - Global search results page
   - Displays search results from global search functionality

#### Authentication Pages (Excluded from count):
- Login (`/login`)
- Register (`/register`)
- Forgot Password (`/forgot-password`)
- Reset Password (`/reset-password`)
- Two Factor (`/2fa`)

**Implementation Files**:
- All pages: `travelvisitationagencyfe/src/pages/`
- Route definitions: `travelvisitationagencyfe/src/routes/AppRoutes.jsx`

---

### 3. The system should have a dashboard with a business information summary. (4 pts)

**Status**: ✅ **IMPLEMENTED**

#### Implementation Details:

**Frontend**: `travelvisitationagencyfe/src/pages/Dashboard.jsx`

The dashboard displays different information based on user role:

**ADMIN Dashboard**:
- Total Users count
- Total Destinations count
- Total Packages count
- Total Bookings count
- Total Payments count

**TOURIST Dashboard**:
- Welcome message with username
- Available Destinations count
- Available Packages count
- My Bookings count
- Pending Payments count

#### Features:
- **Real-time Updates**: Dashboard refreshes when window regains focus
- **Role-based Display**: Different stats shown based on user role
- **Visual Cards**: Each stat displayed in a card with icon and color coding
- **Loading States**: Shows loading indicator while fetching data

#### Backend Implementation:

**Service**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/DashboardService.java`
- `getAdminStats()`: Returns counts for all entities
- `getTouristStats(Long userId)`: Returns user-specific stats

**Controller**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/DashboardController.java`
- `GET /api/dashboard/stats`: Returns dashboard statistics based on user role

**Frontend Service**: `travelvisitationagencyfe/src/services/dashboardService.js`
- `getDashboardStats(role)`: Fetches stats from backend

**Key Files**:
- Frontend: `travelvisitationagencyfe/src/pages/Dashboard.jsx`
- Backend Service: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/DashboardService.java`
- Backend Controller: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/DashboardController.java`
- Frontend Service: `travelvisitationagencyfe/src/services/dashboardService.js`

---

### 4. The system should use pagination when displaying table data. (3 pts)

**Status**: ✅ **IMPLEMENTED**

#### Implementation Details:

**Reusable Table Component**: `travelvisitationagencyfe/src/components/Table.jsx`

The `Table` component provides:
- **Pagination Controls**: Previous/Next buttons, page number display
- **Page Size Selection**: Dropdown to choose 5, 10, or 20 items per page
- **Client-side Pagination**: Data is paginated on the frontend after filtering
- **Search Integration**: Search works with pagination

#### How It Works:

1. **Parent Component** (e.g., `Bookings.jsx`, `Payments.jsx`):
   - Fetches all data (or filtered data)
   - Manages `currentPage`, `totalPages`, `pageSize` state
   - Calculates pagination: `const start = page * size; const end = start + size;`
   - Passes paginated data slice to `Table` component

2. **Table Component**:
   - Receives `currentPage`, `totalPages`, `onPageChange`, `pageSize`, `onPageSizeChange` props
   - Displays pagination controls at the bottom
   - Handles page navigation and size changes

#### Example Implementation (Bookings.jsx):

```javascript
const [currentPage, setCurrentPage] = useState(0);
const [pageSize, setPageSize] = useState(5);

// After filtering data
const start = currentPage * pageSize;
const end = start + pageSize;
const paginated = filtered.slice(start, end);
const totalPages = Math.ceil(filtered.length / pageSize);

<Table
  data={paginated}
  currentPage={currentPage}
  totalPages={totalPages}
  onPageChange={setCurrentPage}
  pageSize={pageSize}
  onPageSizeChange={setPageSize}
  // ... other props
/>
```

#### Pages Using Pagination:
- ✅ Destinations
- ✅ Locations
- ✅ Packages
- ✅ Bookings
- ✅ Payments
- ✅ Users

**Key Files**:
- Table Component: `travelvisitationagencyfe/src/components/Table.jsx`
- Example Usage: `travelvisitationagencyfe/src/pages/Bookings.jsx` (lines 34-100)

---

### 5. The system should have a way to reset the password using email. (4 pts)

**Status**: ✅ **IMPLEMENTED**

#### Implementation Details:

**Flow**:
1. User enters email on Forgot Password page
2. System generates 6-digit code and sends via email
3. User enters code to verify
4. System generates temporary token
5. User enters new password with token
6. Password is updated

#### Frontend Implementation:

**Pages**:
1. **ForgotPassword.jsx** (`travelvisitationagencyfe/src/pages/auth/ForgotPassword.jsx`)
   - Two-step form: Email → Code verification
   - Validates email format
   - Handles code input (6 digits)

2. **ResetPassword.jsx** (`travelvisitationagencyfe/src/pages/auth/ResetPassword.jsx`)
   - Single form for new password
   - Validates password strength
   - Uses token from URL query parameter

**Service**: `travelvisitationagencyfe/src/services/authService.js`
- `forgotPassword(email)`: Sends reset code
- `verifyPasswordResetCode(email, code)`: Verifies code and returns token
- `resetPassword(token, newPassword)`: Resets password with token

#### Backend Implementation:

**Controller**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/AuthController.java`
- `POST /api/auth/forgot-password`: Generates and sends reset code
- `POST /api/auth/verify-password-reset-code`: Verifies code and returns token
- `POST /api/auth/reset-password`: Updates password with token

**Service**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/AuthService.java`
- `forgotPassword(String email)`: 
  - Generates 6-digit code
  - Saves to `PasswordResetCode` entity (expires in 10 minutes)
  - Sends email via `EmailService`
  - Returns generic message (prevents email enumeration)

- `verifyPasswordResetCode(String email, String code)`:
  - Validates code and expiration
  - Generates temporary token (expires in 10 minutes)
  - Saves to `PasswordResetToken` entity
  - Returns token for password reset

- `resetPassword(String token, String newPassword)`:
  - Validates token and expiration
  - Encodes new password with BCrypt
  - Updates user password
  - Deletes token

**Entities**:
- `PasswordResetCode`: Stores reset codes with expiration
- `PasswordResetToken`: Stores temporary tokens for password reset

**Email Service**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/EmailService.java`
- `sendPasswordResetCode(String email, String code)`: Sends reset code email

**Key Files**:
- Frontend Pages: 
  - `travelvisitationagencyfe/src/pages/auth/ForgotPassword.jsx`
  - `travelvisitationagencyfe/src/pages/auth/ResetPassword.jsx`
- Frontend Service: `travelvisitationagencyfe/src/services/authService.js`
- Backend Controller: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/AuthController.java`
- Backend Service: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/AuthService.java`
- Email Service: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/EmailService.java`

---

### 6. The system should use two-factor authentication for login using email or any other tool like Google authentication. (5 pts)

**Status**: ✅ **IMPLEMENTED** - Uses email-based 2FA

#### Implementation Details:

**Flow**:
1. User enters username/email and password
2. If 2FA is enabled, system generates 6-digit code
3. Code is sent to user's email
4. System returns temporary token (not full auth token)
5. User enters code on 2FA page
6. System verifies code and returns full authentication token
7. User is logged in

#### Frontend Implementation:

**Pages**:
1. **Login.jsx** (`travelvisitationagencyfe/src/pages/auth/Login.jsx`)
   - Handles initial login
   - If 2FA required, stores temp token and redirects to `/2fa`
   - Clears auth state on mount to force fresh login

2. **TwoFactor.jsx** (`travelvisitationagencyfe/src/pages/auth/TwoFactor.jsx`)
   - Displays 2FA code input form
   - Validates 6-digit code
   - Verifies code with backend
   - Stores auth token and redirects to dashboard

**Service**: `travelvisitationagencyfe/src/services/authService.js`
- `login(usernameOrEmail, password)`: Returns response with `requires2FA` flag and `tempToken`
- `verify2fa(tempToken, code)`: Verifies code and returns full auth token

**Route Protection**: `travelvisitationagencyfe/src/routes/TwoFactorRoute.jsx`
- Protects `/2fa` route
- Requires temp token to access

#### Backend Implementation:

**Controller**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/AuthController.java`
- `POST /api/auth/login`: Returns `LoginResponse` with `requires2FA` and `tempToken` if 2FA enabled
- `POST /api/auth/verify-2fa`: Verifies code and returns full auth token

**Service**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/AuthService.java`

**Login Method** (`login(String usernameOrEmail, String password)`):
- Validates credentials
- If 2FA enabled:
  - Generates 6-digit code
  - Saves to `TwoFactorCode` entity (expires in 5 minutes)
  - Sends email via `EmailService`
  - Creates temporary `AuthToken` (expires in 10 minutes)
  - Returns `LoginResponse` with `requires2FA=true` and `tempToken`
- If 2FA disabled:
  - Creates full `AuthToken` (expires in 8 hours)
  - Returns `LoginResponse` with `requires2FA=false` and `token`

**Verify 2FA Method** (`verify2fa(String tempToken, String code)`):
- Validates temp token and expiration
- Retrieves latest 2FA code for user
- Validates code and expiration
- Deletes temp token and 2FA code
- Creates full `AuthToken` (expires in 8 hours)
- Returns `LoginResponse` with full token

**Entities**:
- `TwoFactorCode`: Stores 2FA codes with expiration (5 minutes)
- `AuthToken`: Stores authentication tokens (temp: 10 min, full: 8 hours)

**Email Service**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/EmailService.java`
- `send2FACode(String email, String code)`: Sends 2FA code email

**User Profile**: Users can enable/disable 2FA from Profile page
- `User.twoFactorEnabled`: Boolean flag
- `User.twoFactorEmail`: Email address for 2FA codes

**Key Files**:
- Frontend Pages:
  - `travelvisitationagencyfe/src/pages/auth/Login.jsx`
  - `travelvisitationagencyfe/src/pages/auth/TwoFactor.jsx`
- Frontend Service: `travelvisitationagencyfe/src/services/authService.js`
- Route Protection: `travelvisitationagencyfe/src/routes/TwoFactorRoute.jsx`
- Backend Controller: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/AuthController.java`
- Backend Service: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/AuthService.java`
- Email Service: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/EmailService.java`

---

### 7. The system should implement a global search. Example: refer to the search on the React resource Quick Start – React. (6 pts)

**Status**: ✅ **IMPLEMENTED**

#### Implementation Details:

**Global Search** is implemented as a search bar in the top navigation that searches across multiple entities simultaneously.

#### Frontend Implementation:

**Topbar Component**: `travelvisitationagencyfe/src/components/layout/Topbar.jsx`
- Search input in top navigation bar
- **Debouncing**: 300ms delay to reduce API calls
- **Minimum Query Length**: 2 characters
- **Dropdown Results**: Shows results in dropdown below search bar
- **Keyboard Navigation**: Arrow keys to navigate, Enter to select, Escape to close
- **Grouped Results**: Results grouped by entity type (Destinations, Packages, etc.)
- **Click to Navigate**: Clicking result navigates to relevant page

**Features**:
- Real-time search as user types (with debouncing)
- Loading indicator while searching
- Results grouped by type with color-coded badges
- Click outside to close dropdown
- Keyboard navigation support

**Service**: `travelvisitationagencyfe/src/services/globalSearchService.js`
- `globalSearch(query)`: Calls backend API `/api/search?query={query}`
- Returns array of `SearchResult` objects

**Search Results Page**: `travelvisitationagencyfe/src/pages/Search.jsx`
- Alternative page for displaying search results
- Route: `/search`

#### Backend Implementation:

**Controller**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/GlobalSearchController.java`
- `GET /api/search?query={searchTerm}`: 
  - Extracts user ID and role from Authorization header
  - Calls `GlobalSearchService.search()`
  - Returns list of `SearchResult` DTOs

**Service**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/GlobalSearchService.java`

**Search Logic**:
- Searches across multiple entities:
  1. **Destinations**: Name, category, summary, rating (numeric)
  2. **Tour Packages**: Title, description, price (BigDecimal), duration (int), max people (int)
  3. **Bookings**: Package title, user name, dates (LocalDate), people count (int)
  4. **Payments**: Reference, package title, amount (BigDecimal), paid date (LocalDate)
  5. **Users** (ADMIN only): Full name, email, username
  6. **Locations** (ADMIN only): Name, code

**Advanced Search Features**:
- **Text Search**: Case-insensitive partial matching
- **Numeric Search**: 
  - Parses query as `Double`, `BigDecimal`, `Integer`
  - Checks string representation for partial matches
  - Performs exact numeric comparisons
  - Uses `BigDecimal.toPlainString()` for prices to avoid scientific notation
- **Date Search**:
  - Parses query as `LocalDate` (multiple formats: yyyy-MM-dd, dd/MM/yyyy, etc.)
  - Checks string representation for partial matches (e.g., "2024" in "2024-01-15")
  - Performs exact date comparisons

**Role-Based Access**:
- Tourists: See only their own bookings and payments
- Admins: See all bookings, payments, users, and locations

**DTO**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/dto/SearchResult.java`
- Fields: `type`, `id`, `title`, `description`, `route`
- Used to return standardized search results

**Key Files**:
- Frontend Component: `travelvisitationagencyfe/src/components/layout/Topbar.jsx` (lines 8-244)
- Frontend Service: `travelvisitationagencyfe/src/services/globalSearchService.js`
- Backend Controller: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/controller/GlobalSearchController.java`
- Backend Service: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/service/GlobalSearchService.java`
- DTO: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/dto/SearchResult.java`

---

### 8. The system should have a way to search in the table list by any of the values of the table columns. (4 pts)

**Status**: ✅ **IMPLEMENTED**

#### Implementation Details:

**Reusable Table Component**: `travelvisitationagencyfe/src/components/Table.jsx`

The `Table` component includes a built-in search bar that:
- Searches across all visible columns
- Filters data in real-time as user types
- Works with pagination (searches all data, then paginates results)

#### How It Works:

1. **Table Component** (`travelvisitationagencyfe/src/components/Table.jsx`):
   - Has internal `searchTerm` state
   - Renders search input with search icon
   - Calls `onSearch` callback when search term changes
   - Parent component handles filtering logic

2. **Parent Component** (e.g., `Bookings.jsx`):
   - Receives search term from `Table` component via `onSearch` callback
   - Filters all data based on search term
   - Converts all fields to strings for comparison
   - Handles special cases (dates, numbers)

#### Example Implementation (Bookings.jsx):

```javascript
const handleSearch = (term) => {
  setSearchTerm(term);
  setCurrentPage(0); // Reset to first page
};

// In fetchBookings or useEffect
let filtered = allBookingsList;
if (search && search.trim()) {
  const term = search.toLowerCase();
  filtered = allBookingsList.filter(b => {
    // Convert all fields to searchable strings
    const packageTitle = b.tourPackage?.title?.toLowerCase() || '';
    const userName = b.user?.fullName?.toLowerCase() || '';
    const statusStr = b.status?.toLowerCase() || '';
    const peopleCountStr = b.peopleCount?.toString() || '';
    
    // Handle dates - multiple formats
    let startDateStr = '';
    if (b.startDate) {
      const startDate = new Date(b.startDate);
      startDateStr = [
        startDate.toISOString().split('T')[0],
        startDate.toLocaleDateString(),
        // ... more formats
      ].join(' ').toLowerCase();
    }
    
    // Check if any field contains search term
    return packageTitle.includes(term) ||
           userName.includes(term) ||
           statusStr.includes(term) ||
           peopleCountStr.includes(term) ||
           startDateStr.includes(term);
  });
}
```

#### Search Features by Page:

**Bookings** (`travelvisitationagencyfe/src/pages/Bookings.jsx`):
- Searches: Package title, user name, status, people count, start date, end date
- Date formats: ISO (2024-01-15), locale formats

**Payments** (`travelvisitationagencyfe/src/pages/Payments.jsx`):
- Searches: Reference, package title, amount, status, paid date
- Date formats: Multiple formats supported

**Locations** (`travelvisitationagencyfe/src/pages/Locations.jsx`):
- Searches: Name, code, type

**Packages** (`travelvisitationagencyfe/src/pages/Packages.jsx`):
- Searches: Title, description, price, duration, max people
- Numeric fields converted to strings for search

**Destinations** (`travelvisitationagencyfe/src/pages/Destinations.jsx`):
- Searches: Name, category, summary, rating
- Rating converted to string for search

**Users** (`travelvisitationagencyfe/src/pages/Users.jsx`):
- Searches: Full name, email, username, role, phone

#### Key Features:
- ✅ Searches all columns (text, numbers, dates)
- ✅ Case-insensitive
- ✅ Partial matching (contains)
- ✅ Works with pagination (searches all, then paginates)
- ✅ Real-time filtering as user types
- ✅ Resets to first page on new search

**Key Files**:
- Table Component: `travelvisitationagencyfe/src/components/Table.jsx` (lines 20-26, 36-49)
- Example Implementations:
  - `travelvisitationagencyfe/src/pages/Bookings.jsx` (lines 34-96)
  - `travelvisitationagencyfe/src/pages/Payments.jsx`
  - `travelvisitationagencyfe/src/pages/Locations.jsx`
  - `travelvisitationagencyfe/src/pages/Packages.jsx`
  - `travelvisitationagencyfe/src/pages/Destinations.jsx`
  - `travelvisitationagencyfe/src/pages/Users.jsx`

---

### 9. The system should have role-based authentication. Admin and other roles based on your project idea. (5 pts)

**Status**: ✅ **IMPLEMENTED**

#### Implementation Details:

**Roles**:
- **ADMIN**: Full system access
- **TOURIST**: Limited access (own bookings/payments, view packages/destinations)

#### Frontend Implementation:

**Auth Utilities**: `travelvisitationagencyfe/src/utils/auth.js`
- `getUserRole()`: Returns current user's role from localStorage
- `isAuthenticated()`: Checks if user has valid token
- `getCurrentUser()`: Returns current user object

**Protected Routes**: `travelvisitationagencyfe/src/routes/ProtectedRoute.jsx`
- Wraps routes that require authentication
- `allowedRoles` prop: Array of roles allowed to access route
- Redirects to `/login` if not authenticated
- Redirects to `/dashboard` if role not allowed

**Route Configuration**: `travelvisitationagencyfe/src/routes/AppRoutes.jsx`
```javascript
<Route element={<ProtectedRoute />}>
  <Route element={<AppLayout />}>
    {/* All authenticated users */}
    <Route path="/dashboard" element={<Dashboard />} />
    <Route path="/destinations" element={<Destinations />} />
    <Route path="/packages" element={<Packages />} />
    <Route path="/bookings" element={<Bookings />} />
    <Route path="/payments" element={<Payments />} />
    
    {/* ADMIN only */}
    <Route element={<ProtectedRoute allowedRoles={["ADMIN"]} />}>
      <Route path="/users" element={<Users />} />
      <Route path="/locations" element={<Locations />} />
    </Route>
    
    {/* All authenticated users */}
    <Route path="/profile" element={<Profile />} />
  </Route>
</Route>
```

**Sidebar**: `travelvisitationagencyfe/src/components/layout/Sidebar.jsx`
- Conditionally renders menu items based on role
- ADMIN sees: Dashboard, Destinations, Locations, Packages, Bookings, Payments, Users, Profile
- TOURIST sees: Dashboard, Destinations, Packages, My Bookings, Payments, Profile

**Page-Level Role Checks**:
- Pages check role to show/hide features
- Example: `Bookings.jsx` shows all bookings for ADMIN, only own bookings for TOURIST
- Example: `Payments.jsx` filters payments by user for TOURIST

#### Backend Implementation:

**User Entity**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/User.java`
- `role` field: Enum of type `Role`
- Values: `ADMIN`, `TOURIST`

**Role Enum**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/enums/Role.java`
```java
public enum Role {
    ADMIN,
    TOURIST
}
```

**Security Configuration**: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/config/WebSecurityConfig.java`
- Configures Spring Security
- Protects endpoints based on authentication
- Token-based authentication

**Service-Level Role Checks**:
- Services check user role to filter data
- Example: `GlobalSearchService` only searches users/locations for ADMIN
- Example: `BookingService` filters bookings by user for TOURIST
- Example: `PaymentService` filters payments by user for TOURIST

**Controller-Level Role Checks**:
- Some endpoints check role in controller
- Example: User management endpoints (ADMIN only)

**Token-Based Authentication**:
- JWT-like token stored in `AuthToken` entity
- Token includes user ID and role
- Frontend stores token in localStorage
- Backend validates token on each request

#### Role-Based Features:

**ADMIN Can**:
- View all users, locations, destinations, packages, bookings, payments
- Create/edit/delete all entities
- Access Users and Locations pages
- See all data in global search

**TOURIST Can**:
- View destinations and packages
- Create bookings
- View own bookings and payments
- Edit own profile
- See only own data in global search

**Key Files**:
- Frontend Auth Utils: `travelvisitationagencyfe/src/utils/auth.js`
- Protected Routes: `travelvisitationagencyfe/src/routes/ProtectedRoute.jsx`
- Route Config: `travelvisitationagencyfe/src/routes/AppRoutes.jsx`
- Sidebar: `travelvisitationagencyfe/src/components/layout/Sidebar.jsx`
- Backend User Model: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/User.java`
- Role Enum: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/model/enums/Role.java`
- Security Config: `TravelVisitationAgency/src/main/java/auca/ac/rw/travel/config/WebSecurityConfig.java`

---

## Code Reusability

**Status**: ✅ **IMPLEMENTED** - The system follows the requirement: "one button, one sidebar, one top bar, and one footer. Code reusability is a key."

### Reusable Components:

#### 1. **Button Component** (`travelvisitationagencyfe/src/components/ui/Button.jsx`)
- Single reusable button component used throughout the application
- Props: `variant` (primary/secondary), `type`, `onClick`, `disabled`, `className`
- Used in: All forms, modals, and action buttons across the application

#### 2. **Sidebar Component** (`travelvisitationagencyfe/src/components/layout/Sidebar.jsx`)
- Single sidebar component used in the main layout
- Role-aware navigation (shows different items for ADMIN vs TOURIST)
- Used in: `AppLayout` component (wraps all authenticated pages)

#### 3. **Topbar Component** (`travelvisitationagencyfe/src/components/layout/Topbar.jsx`)
- Single top navigation bar component
- Includes: Global search, user profile dropdown, menu toggle (mobile)
- Used in: `AppLayout` component (wraps all authenticated pages)

#### 4. **Footer Component** (`travelvisitationagencyfe/src/components/layout/Footer.jsx`)
- Single footer component
- Used in: `AppLayout` component (wraps all authenticated pages)

#### 5. **Table Component** (`travelvisitationagencyfe/src/components/Table.jsx`)
- Highly reusable table component
- Features: Search, pagination, edit/delete actions, custom actions, row styling
- Used in: All pages that display tabular data (Destinations, Locations, Packages, Bookings, Payments, Users)

#### 6. **Modal Component** (`travelvisitationagencyfe/src/components/Modal.jsx`)
- Reusable modal/dialog component
- Used in: All pages for create/edit forms

#### 7. **ConfirmationModal Component** (`travelvisitationagencyfe/src/components/ConfirmationModal.jsx`)
- Reusable confirmation dialog
- Used in: Delete confirmations, cancel actions

#### 8. **AppLayout Component** (`travelvisitationagencyfe/src/components/layout/AppLayout.jsx`)
- Main layout wrapper
- Combines: Sidebar, Topbar, Footer
- Uses React Router `Outlet` to render child routes
- Used in: All authenticated pages via route configuration

### Layout Structure:

```
AppLayout
├── Sidebar (reusable)
├── Topbar (reusable)
│   └── Global Search
├── Main Content (Outlet - renders page components)
└── Footer (reusable)
```

**Key Files**:
- Button: `travelvisitationagencyfe/src/components/ui/Button.jsx`
- Sidebar: `travelvisitationagencyfe/src/components/layout/Sidebar.jsx`
- Topbar: `travelvisitationagencyfe/src/components/layout/Topbar.jsx`
- Footer: `travelvisitationagencyfe/src/components/layout/Footer.jsx`
- Table: `travelvisitationagencyfe/src/components/Table.jsx`
- Modal: `travelvisitationagencyfe/src/components/Modal.jsx`
- AppLayout: `travelvisitationagencyfe/src/components/layout/AppLayout.jsx`

---

## Architecture Overview

### Frontend Architecture:

```
travelvisitationagencyfe/
├── src/
│   ├── index.js                    # Entry point
│   ├── App.js                       # Root component
│   ├── routes/
│   │   ├── AppRoutes.jsx           # Route definitions
│   │   ├── ProtectedRoute.jsx     # Auth protection
│   │   └── TwoFactorRoute.jsx     # 2FA protection
│   ├── pages/
│   │   ├── Dashboard.jsx
│   │   ├── Destinations.jsx
│   │   ├── Locations.jsx
│   │   ├── Packages.jsx
│   │   ├── Bookings.jsx
│   │   ├── Payments.jsx
│   │   ├── Users.jsx
│   │   ├── Profile.jsx
│   │   ├── Search.jsx
│   │   └── auth/
│   │       ├── Login.jsx
│   │       ├── Register.jsx
│   │       ├── ForgotPassword.jsx
│   │       ├── ResetPassword.jsx
│   │       └── TwoFactor.jsx
│   ├── components/
│   │   ├── layout/
│   │   │   ├── AppLayout.jsx       # Main layout
│   │   │   ├── Sidebar.jsx        # Reusable sidebar
│   │   │   ├── Topbar.jsx         # Reusable topbar
│   │   │   └── Footer.jsx         # Reusable footer
│   │   ├── ui/
│   │   │   └── Button.jsx         # Reusable button
│   │   ├── Table.jsx              # Reusable table
│   │   ├── Modal.jsx              # Reusable modal
│   │   └── ConfirmationModal.jsx  # Reusable confirmation
│   ├── services/
│   │   ├── api.js                 # Axios instance with interceptors
│   │   ├── authService.js
│   │   ├── bookingService.js
│   │   ├── paymentService.js
│   │   ├── dashboardService.js
│   │   ├── globalSearchService.js
│   │   └── ...
│   └── utils/
│       └── auth.js                 # Auth utilities
```

### Backend Architecture:

```
TravelVisitationAgency/
├── src/main/java/auca/ac/rw/travel/
│   ├── model/                      # JPA Entities
│   │   ├── User.java
│   │   ├── Location.java
│   │   ├── Destination.java
│   │   ├── TourPackage.java
│   │   ├── Booking.java
│   │   ├── Payment.java
│   │   └── enums/
│   │       ├── Role.java
│   │       ├── BookingStatus.java
│   │       ├── PaymentStatus.java
│   │       └── LocationType.java
│   ├── repository/                 # JPA Repositories
│   │   ├── UserRepository.java
│   │   ├── LocationRepository.java
│   │   ├── DestinationRepository.java
│   │   ├── TourPackageRepository.java
│   │   ├── BookingRepository.java
│   │   └── PaymentRepository.java
│   ├── service/                    # Business Logic
│   │   ├── AuthService.java
│   │   ├── UserService.java
│   │   ├── BookingService.java
│   │   ├── PaymentService.java
│   │   ├── DashboardService.java
│   │   ├── GlobalSearchService.java
│   │   └── EmailService.java
│   ├── controller/                 # REST Controllers
│   │   ├── AuthController.java
│   │   ├── UserController.java
│   │   ├── BookingController.java
│   │   ├── PaymentController.java
│   │   ├── DashboardController.java
│   │   └── GlobalSearchController.java
│   ├── dto/                        # Data Transfer Objects
│   │   ├── LoginRequest.java
│   │   ├── LoginResponse.java
│   │   └── SearchResult.java
│   └── config/
│       └── WebSecurityConfig.java  # Spring Security Config
```

### Data Flow:

1. **User Action** → Frontend Component
2. **Component** → Service (API call)
3. **Service** → Backend Controller (REST endpoint)
4. **Controller** → Service (business logic)
5. **Service** → Repository (database access)
6. **Repository** → Database (PostgreSQL)
7. **Response flows back** through the same layers

### Authentication Flow:

1. User logs in → `POST /api/auth/login`
2. If 2FA enabled → Temp token returned, code sent via email
3. User enters code → `POST /api/auth/verify-2fa`
4. Full token returned → Stored in localStorage
5. All subsequent requests include token in `Authorization: Bearer {token}` header
6. Backend validates token on each request

---

## Summary

This Travel Visitation Agency system is a comprehensive full-stack application that meets all exam requirements:

✅ **5+ Entities**: 6 main entities (User, Location, Destination, TourPackage, Booking, Payment)
✅ **5+ Pages**: 9 main pages (excluding auth pages)
✅ **Dashboard**: Business information summary with role-based views
✅ **Pagination**: Implemented in reusable Table component across all pages
✅ **Password Reset**: Email-based reset with code verification
✅ **Two-Factor Authentication**: Email-based 2FA with temporary tokens
✅ **Global Search**: Backend API with frontend dropdown, searches all entities, supports text/numbers/dates
✅ **Table Search**: Built into reusable Table component, searches all columns
✅ **Role-Based Authentication**: ADMIN and TOURIST roles with different access levels
✅ **Code Reusability**: One button, one sidebar, one topbar, one footer, reusable table/modal components

The system follows best practices for code organization, separation of concerns, and maintainability.
