<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Wing ID Group Report with Position and Grade</title>
    <style>
        /* --- General Styles --- */
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; background-color: #f0f4f7; display: flex; justify-content: center; align-items: flex-start; min-height: 100vh; padding: 40px 20px; }
        .page-container { background-color: #fff; padding: 40px; border-radius: 12px; box-shadow: 0 8px 25px rgba(0, 0, 0, 0.1); width: 100%; max-width: 1000px; } 
        h1, h2, h3 { color: #2c3e50; text-align: center; }
        
        /* --- Cover Page Styles --- */
        #coverPage h1 { font-size: 2.2em; margin-bottom: 20px; color: #3498db; }
        #coverPage p { line-height: 1.6; color: #555; margin-bottom: 25px; text-align: justify; }
        .intro-btn { background-color: #2ecc71; color: white; padding: 15px 30px; border: none; border-radius: 8px; cursor: pointer; font-size: 1.1em; margin-top: 15px; display: block; width: 60%; margin-left: auto; margin-right: auto; transition: background-color 0.3s; }
        .intro-btn:hover { background-color: #27ae60; }

        /* --- Search Page Styles --- */
        #searchPage h2 { margin-bottom: 30px; }
        .search-area { background-color: #f9f9f9; padding: 20px; border-radius: 8px; margin-bottom: 30px; border: 1px solid #ddd; display: flex; justify-content: center; align-items: center; }
        .search-area label { margin-right: 15px; font-weight: 600; }
        .search-area input[type="text"] { width: 40%; padding: 10px; border: 1px solid #ccc; border-radius: 4px; margin-right: 10px; }
        .search-area button { width: 20%; background-color: #e67e22; color: white; padding: 10px; border: none; border-radius: 4px; cursor: pointer; transition: background-color 0.3s; }
        .search-area button:hover { background-color: #d35400; }

        /* --- Report Display Styles --- */
        #wingReportDisplay { margin-top: 20px; overflow-x: auto; }
        .report-table { width: 100%; border-collapse: collapse; margin-top: 15px; min-width: 900px; }
        .report-table th, .report-table td { border: 1px solid #ddd; padding: 10px; text-align: left; }
        .report-table th { background-color: #34495e; color: white; font-weight: 600; }
        .report-table tr:nth-child(even) { background-color: #f2f2f2; }
        .error { color: #e74c3c; font-weight: bold; text-align: center; padding: 15px; background-color: #fbecec; border-radius: 4px;}
        .report-header { margin-bottom: 15px; font-size: 1.2em; font-weight: bold; }
        
        /* Grade/Position Styling (kept for final columns) */
        .grade-A { background-color: #e6ffe6; font-weight: bold; } 
        .grade-C { background-color: #ffffcc; }
        .position-highlight { font-weight: bold; color: #1e8449; text-align: center; }

        /* Utility to hide pages */
        .hidden { display: none; }
    </style>
</head>
<body>

<div class="page-container">
    
    <div id="coverPage">
        <h1>Student Program Report System</h1>
        <p>This system provides a detailed breakdown of student performance including their Wing Base and program grades. Enter a **Wing ID** (e.g., Alpha Wing, Beta Wing) on the next page to view the group report.</p>

        <h3>Program Overview</h3>
        <p>Results are categorized by **Wing Base**. The group report displays each student's **Overall Position** within that Wing and their calculated **Final Grade**.</p>
        
        <button class="intro-btn" onclick="showPage('searchPage')">Proceed to Search</button>
    </div>

    <div id="searchPage" class="hidden">
        <h2>Wing Group Report Search</h2>
        
        <div class="search-area">
            <label>Enter Wing ID:</label>
            <input type="text" id="wingIdInput" placeholder="e.g., Alpha Wing" required>
            <button onclick="searchWing()">Search</button>
        </div>

        <div id="wingReportDisplay">
            <p style="text-align: center; color: #7f8c8d;">Enter a Wing ID and click Search to load the report.</p>
        </div>

        <button class="intro-btn" style="width: 30%; margin-top: 30px;" onclick="showPage('coverPage')">Back to Introduction</button>
    </div>
    
</div>

<script>
    // --- 1. DATA STRUCTURE (Data remains the same) ---
    const studentData = {
        "TECH001": { name: "Alex Smith", category: "Engineering Tech", wing: "Alpha Wing", finalGrade: "A+", programs: [
            { name: "Advanced Robotics", grade: "A", position: 1 }, 
            { name: "AI Algorithms", grade: "A-", position: 2 }
        ]},
        "BUS002": { name: "Maria Garcia", category: "Business Management", wing: "Beta Wing", finalGrade: "C+", programs: [
            { name: "Financial Modeling", grade: "B+", position: 2 },
            { name: "Marketing Strategy", grade: "D", position: 4 }
        ]},
        "TECH003": { name: "Ethan Hunt", category: "Engineering Tech", wing: "Alpha Wing", finalGrade: "B-", programs: [
            { name: "Advanced Robotics", grade: "B+", position: 3 }, 
            { name: "Cloud Computing", grade: "A+", position: 1 },
            { name: "Data Structures", grade: "F", position: 5 }
        ]},
        "SCI004": { name: "Ben Carter", category: "Theoretical Science", wing: "Gamma Wing", finalGrade: "A", programs: [
            { name: "Quantum Physics", grade: "A+", position: 1 }, 
            { name: "Theoretical Math", grade: "A", position: 2 }
        ]},
        "MED005": { name: "Sara Lee", category: "Health Studies", wing: "Delta Wing", finalGrade: "B", programs: [
            { name: "Clinical Trials", grade: "B", position: 3 }
        ]},
        "BUS006": { name: "John Doe", category: "Business Management", wing: "Beta Wing", finalGrade: "A", programs: [
            { name: "Financial Modeling", grade: "A+", position: 1 },
            { name: "Marketing Strategy", grade: "A", position: 1 }
        ]},
    };

    // --- 2. NAVIGATION LOGIC (Unchanged) ---
    function showPage(pageId) {
        document.getElementById('coverPage').classList.add('hidden');
        document.getElementById('searchPage').classList.add('hidden'); 
        
        if (pageId === 'searchPage') {
            document.getElementById('wingIdInput').value = '';
            document.getElementById('wingReportDisplay').innerHTML = '<p style="text-align: center; color: #7f8c8d;">Enter a Wing ID and click Search to load the report.</p>';
        }

        document.getElementById(pageId).classList.remove('hidden');
    }

    // --- 3. WING SEARCH LOGIC (Updated to remove grade/position from program details) ---
    function searchWing() {
        const inputWing = document.getElementById('wingIdInput').value.trim();
        const reportDiv = document.getElementById('wingReportDisplay');
        
        if (!inputWing) {
            reportDiv.innerHTML = '<div class="error">Please enter a valid Wing ID (e.g., Alpha Wing).</div>';
            return;
        }

        // 1. Filter students
        let filteredStudents = Object.keys(studentData)
            .filter(id => studentData[id].wing.toLowerCase() === inputWing.toLowerCase())
            .map(id => ({ id: id, ...studentData[id] }));

        if (filteredStudents.length === 0) {
            reportDiv.innerHTML = `<div class="error">No students found in the **${inputWing}**. Please check the Wing ID.</div>`;
            return;
        }

        // 2. Assign overall position (based on alphabetical sort for demo)
        filteredStudents.sort((a, b) => a.name.localeCompare(b.name));
        
        // --- Generate Group Report ---

        // Build the HTML table header
        let tableHtml = `
            <div class="report-header">Results for ${inputWing} (${filteredStudents.length} Students)</div>
            <table class="report-table">
                <thead>
                    <tr>
                        <th>Student ID</th>
                        <th>Name</th>
                        <th>Category</th>
                        <th>Programs Enrolled</th> <th>Overall Position</th>
                        <th>Final Grade</th>
                    </tr>
                </thead>
                <tbody>
        `;

        // 3. Populate the table rows with student data
        filteredStudents.forEach((student, index) => {
            const overallPosition = index + 1; // Rank based on the sort order (1-based index)
            
            // --- MODIFIED LINE ---
            // Format the list of programs, including ONLY the program name.
            const programsList = student.programs.map(p => p.name).join('<br>');

            // Apply light styling based on final grade
            const gradeClass = student.finalGrade.startsWith('A') ? 'grade-A' : 
                               student.finalGrade.startsWith('C') ? 'grade-C' : '';

            tableHtml += `
                <tr>
                    <td>${student.id}</td>
                    <td>${student.name}</td>
                    <td>${student.category}</td>
                    <td>${programsList}</td> <td class="position-highlight">${overallPosition}</td>
                    <td class="${gradeClass}">${student.finalGrade}</td>
                </tr>
            `;
        });

        tableHtml += `
                </tbody>
            </table>
        `;

        reportDiv.innerHTML = tableHtml;
    }
    
    // Initialize: Ensure only the cover page is visible on load
    document.addEventListener('DOMContentLoaded', () => {
        showPage('coverPage');
    });

</script>

</body>
</html>
