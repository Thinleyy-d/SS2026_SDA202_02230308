# Practical 2 - UML Class Diagram & UML Object Diagram

## UML Class Diagram

### Overview
The class diagram shows the structure of the automated grading system, including main classes, their roles, and relationships.

### Diagram

![alt text](<UML class diagram.png>)

### Key Classes
- Student → submits assignments, views results, resubmits  
- Professor → sets deadlines, defines criteria, reviews and modifies grades  
- Admin → manages users and system settings  
- Assignment → stores assignment details (title, deadline)  
- Submission → represents student submissions  
- GradingSystem → handles automation (run code, evaluate, save results)  
- Result → stores score and feedback  
- PlagiarismService → checks copied content  
- LMS → stores final results  

### Relationships
- Student → Submission → Assignment  
- GradingSystem → processes submissions and generates results  
- Professor → reviews results  
- GradingSystem → uses PlagiarismService and LMS  

### Summary
The class diagram defines the static structure and shows how responsibilities are divided between users and the system.

---

## UML Object Diagram

### Overview
The object diagram shows a real example of the system at runtime using actual instances.

### Diagram

![alt text](<UML object diagram.png>)

### Example Objects
- `student1` → Karma  
- `assignment1` → Java Project  
- `submission1` → submitted work  
- `result1` → score and feedback  
- `professor1` → Dr. Wangchuk  

### Relationships
- student1 → submission1  
- submission1 → assignment1  
- submission1 → result1  
- professor1 → assignment1  

### Summary
The object diagram represents a snapshot of real data, helping to understand how the system works in practice.