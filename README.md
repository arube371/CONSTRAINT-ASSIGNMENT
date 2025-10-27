# CONSTRAINT-ASSIGNMENT
-- ...existing code...

/* Part A: create Programme and Student tables enforcing the rules required at creation */
CREATE TABLE Programme (
    Prog_ID VARCHAR(10) NOT NULL PRIMARY KEY,
    Prog_name VARCHAR(100),
    Campus VARCHAR(100)
);

CREATE TABLE Student (
    Regno VARCHAR(20) NOT NULL PRIMARY KEY,                      -- (i) Regno is PK
    Fname VARCHAR(100) NOT NULL,                                  -- will enforce lowercase via CHECK
    Lname VARCHAR(100),
    Course CHAR(4) NOT NULL,                                      -- (iii) exactly 4 chars
    DISTRICT VARCHAR(100),
    Date_of_JOIN DATE NOT NULL,                                   -- (iv) must have a value
    Mobileno VARCHAR(20),
    Email VARCHAR(255),
    Fees INT,
    Prog_ID VARCHAR(10),
    CONSTRAINT chk_student_fname_lower CHECK (LOWER(Fname) = Fname),     -- (ii) fname lowercase
    CONSTRAINT chk_student_course_len CHECK (CHAR_LENGTH(Course) = 4)
);

-- Part B: append constraints / modifications to Student (assumes the Student table exists)

/* v. Mobile no must have 12 digits */
ALTER TABLE Student
  ADD CONSTRAINT chk_student_mobile12 CHECK (Mobileno REGEXP '^[0-9]{12}$');

/* vi. All emails must have an @ symbol (we will name this constraint so it can be dropped later) */
ALTER TABLE Student
  ADD CONSTRAINT chk_student_email_at CHECK (Email LIKE '%@%');

/* vii. Fees must be in the range 10000 to 50000 */
ALTER TABLE Student
  ADD CONSTRAINT chk_student_fees_range CHECK (Fees BETWEEN 10000 AND 50000);

/* viii. All Regno’s must start with either BSIT/ or BSCS/ */
ALTER TABLE Student
  ADD CONSTRAINT chk_student_regno_prefix CHECK (Regno REGEXP '^(BSIT/|BSCS/)');

/* ix. The prog_ID must be distinct (unique) */
ALTER TABLE Student
  ADD CONSTRAINT uq_student_prog_id UNIQUE (Prog_ID);

/* x. Append another attribute to the student entity and label it Hall_Residence */
ALTER TABLE Student
  ADD COLUMN Hall_Residence VARCHAR(100);

/* xi. Eliminate the constraint that you appended on vi (the email @ check) */
ALTER TABLE Student
  DROP CHECK chk_student_email_at;

-- Part B (xii): Queries to list constraint names and types from the current schema
-- Run these to "evoke" constraint names and types
SELECT
  CONSTRAINT_NAME,
  CONSTRAINT_TYPE,
  TABLE_NAME
FROM information_schema.TABLE_CONSTRAINTS
WHERE CONSTRAINT_SCHEMA = DATABASE()
  AND TABLE_NAME IN ('Student', 'Programme');

SELECT
  CONSTRAINT_NAME,
  CHECK_CLAUSE
FROM information_schema.CHECK_CONSTRAINTS
WHERE CONSTRAINT_SCHEMA = DATABASE()
  AND CONSTRAINT_NAME LIKE 'chk_%';

-- Part C: enforce entity integrity (primary keys already set) and referential integrity
-- Ensure Programme.Prog_ID is primary key (created above). Now add FK on Student.Prog_ID.
ALTER TABLE Student
  ADD CONSTRAINT fk_student_prog
    FOREIGN KEY (Prog_ID) REFERENCES Programme(Prog_ID)
    ON UPDATE CASCADE
    ON DELETE RESTRICT;
-- ...existing code...
