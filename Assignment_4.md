# Schema Design
---------------

### My employee table
```sql
CREATE TABLE employee (
    employee_id     SERIAL PRIMARY KEY,
    first_name      VARCHAR(50) NOT NULL,
    last_name       VARCHAR(50),
    email           VARCHAR(100) NOT NULL UNIQUE,
    phone           VARCHAR(15) UNIQUE,
    hire_date       DATE NOT NULL,
    department_id   INT NOT NULL,
    designation     VARCHAR(100),
    status          VARCHAR(20) DEFAULT 'Active',
    created_at      TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
![Alt Text](https://i.ibb.co/sJCc1ySG/erd.png)

### Full ER Daigram Created By Team

![Alt Text](https://i.ibb.co/HLvfzCSQ/erd3.png)
