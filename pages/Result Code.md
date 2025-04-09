- Individual
	- ```javascript
	  result = (si, rr, ei) =>
	    $.ajax({
	      url: "https://cmc.du.ac.bd/ajax/get_program_by_exam.php?=",
	      method: "POST",
	      timeout: 0,
	      headers: {
	        Accept: "*/*",
	        "Accept-Language": "en-US,en;q=0.5", // this changes every time
	      },
	      data: {
	        reg_no: "" + rr,
	        pro_id: "1",
	        sess_id: si,
	        exam_id: ei,
	        // 976, 3rd prof may 2024 (imran)
	        // 726, 3rd prof nov 2023
	        // 725, 2nd prof nov 2023 (ylma)
	        // 365, 2nd prof nov 2022
	        // 124, 1st prof may 2021
	        gdata: "99",
	      },
	      success: (r) => {
	        cleaned_r = $($.parseHTML(r))[0].innerText;
	        f = rr + resultStatus((resultText = cleaned_r));
	        console.log(f);
	        // console.log(r)
	        // document.body.innerHTML = r;
	        return "result obtained successfully";
	      },
	    });
	  resultStatus = (resultText) => {
	    // if (resultText==' This is not verifyed for this exam. ') {return resultText};
	    // if (resultText==' Results were not published. ') {return resultText};
	    try {
	      const regexPassed = /Passed(.*)/;
	      const regexReferred = /Referred(.*)/;
	      const matchPassed = resultText.match(regexPassed);
	      const regexStudentsName = /(?<=Student's Name)(.*)(?=Registration)/;
	      const studentsName = resultText.match(regexStudentsName)[0].trim();
	      const regexClassRoll = /(?<=Class Roll)(.*)(?=Exam Year)/;
	      const classRoll = resultText.match(regexClassRoll)[0].trim();
	      const regexExamRoll = /(?<=Exam Roll)(.*)(?=Class Roll)/;
	      const examRoll = resultText.match(regexExamRoll)[0].trim();
	      const particulars = `roll :${classRoll} ${studentsName}`;
	      if (matchPassed) {
	        const passedSubjects = matchPassed[1].trim();
	        return `  ${particulars}  , exam roll : ${examRoll} Passed ${passedSubjects}`;
	      }
	      const matchReferred = resultText.match(regexReferred);
	      if (matchReferred) {
	        const referredSubjects = matchReferred[1].trim();
	        return `  ${particulars}  , exam roll : ${examRoll} Referred: ${referredSubjects}`;
	      }
	    } catch {
	      return resultText;
	    }
	  };
	  
	  result(20, 8899, 124); // first variable is registration number and second variable is exam id
	  // third varibale is session id, session id for 19/20 is 20
	  ```
- Bulk
  collapsed:: true
	- ```javascript
	  resultBulk = (sessionId,startReg, endReg, examId) => {for (var i = startReg; i < endReg; i++) {
	    result(sessionId,i, examId);
	  }};
	  ```
- Update Check
	- ```javascript
	  ```