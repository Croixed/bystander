<script>
    import { loop_guard } from "svelte/internal";

  let questionNodes = [
    {
      id: 0,
      question: "Hi, I'm jen \n I may be having a recurring medical event \n Please follow the prompts BEFORE calling 911. \n Will you consent to help me?",
      answers: [
        {
          answer: "Touch here for Yes",
          nextQuestion: 1
        },
        {
          answer: "No", // goto: call 911
          nextQuestion: 12
        }
      ]
    },
    {
      question: "Am I breathing?",
      id: 1,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 2
        },
        {
          answer: "No", // goto: call 911, with appended bit about jen having cerebral palsy, asthma, absence seizures, and periodic partial paralysis
          nextQuestion: 18
        }
      ]
    },
    {
      question: "Does breathing seem difficult?",
      id: 2,
      answers: [
        {
          answer: "No",
          nextQuestion: 3
        },
        {
          answer: "Yes",
          nextQuestion: 10 // goto: asthma instructions: 8?
        }
      ]
    },



    {
      question: "Everything is probably ok. This is likely a regular medical event for Jen not a 911 call message.",
      id: 3,
      answers: [
        {
          answer: "Touch here for next",
          nextQuestion: 4
        }
      ]
    },




    {
      question: "Am I awake/conscious?",
      id: 4,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 5
        },
        {
          answer: "No",
          nextQuestion: 15 // goto: seizure timer instructions
        }
      ]
    },







    {
      question: "am I coherent/making sense?",
      id: 5,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 6
        },
        {
          answer: "No",
          nextQuestion: 7
        }
      ]
    },

    {
      id: 6,
      question: "Try to communicate with me and hit next when told to do so.",
      answers: [
        {
          answer: "Touch here for next",
          nextQuestion: 7
        }
      ]
    },











    {
      question: "Am I Moving?",
      id: 7,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 13 // seizure instructions
        },
        {
          answer: "No",
          nextQuestion: 8 /// am I cold?
        }
      ]

    },
    {
      question: "Do I seem cold?",
      id: 8,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 16
        },
        {
          answer: "No",
          nextQuestion: 9
        }
      ]
    },
    {
      question: "Do I seem warm?",
      id: 9,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 17 // 17 is cooling
        },
        {
          answer: "No",
          nextQuestion: 19 // this is the end, so just show the 911 long message?
        }
      ]
    },









    {
      id: 10,
      question: "Jen has several breathing problems. This is normal for Jen. Please help Jen support the bottom of her ribcage and wait for the timer to end.",
      type: 240,
      answers: [
        {
          answer: "Touch here for next",
          nextQuestion: 11
        }
      ]
    },
    {
      id: 11,
      question: "find ___ for additional help.",
      answers: [
        {
          answer: "Touch here for details",
          nextQuestion: 2000 // update this to later lead to her address info, and later have pin functionality? 
        }
      ]
    },
    {
      id: 12,
      question: "Touch here to call 911.  Please share that Jen might be having a medical event and has recurring health problems. ",
      answers: [
        {
          answer: "Touch here to call 911!",
          nextQuestion: 91111111
        }
      ]

    },






    // 13 and 14 are seizure non timer instructions
    {
      id: 13,
      question: "Jen has atypical absence seizures. This is normal for Jen. Do not call 911 unless she’s convulsing (not normal) or is visibly bleeding. Please remain calm. Being comforting as you might to a child is helpful. If Jen is confused, terrified, or otherwise upset, that’s okay. The seizure has passed and Jen is in recovery mode. Jen may not know herself, may be stuttering, or may be entirely nonverbal. Her brain is rebooting. Water, time, sleep is the only cure. Have her drink some water, and herd her towards one of the wellness rooms once she’s able to stutter in a few minutes. Everything’s fine. ",
      answers: [
        {
          answer: "If Jen is particularly upset, click here to play her some personally soothing audio. <insert link here>", // insert link here
        },
        {
          answer: "Does Jen look confused at communication attempts? Click here to view the sign language alphabet.",
          nextQuestion: 14
        },
        {
          answer: "Have you been preselected as a guardian to help Jen get home? Click here for details", // add pin later
          nextQuestion: 20 // update with address/hotel information later? 
        }
      ]

    },
    {
      id: 14,
      question: " I live across the street at the Residence Inn. Room 524. Key should be in my jacket pocket or the pocket of whatever bag I brought today. After a seizure, I’m a little frightened of things in motion, like elevators or cars. Please don’t use my last name or the word home. Please understand that I currently have no filter and will not remember much later. Please be patient.",
    },






    // seizure timer instructions. should technically comme before 12 and 13 but I'll fix it later(?)
    {
      id: 15,
      type: 30, // I'll probably change type to "timerMinutes" later instead
      question: "Please wait one minute. A link to call 911 will automatically appear after one minute.  If I do not regain consciousness, or am screaming while unconscious, please call 911 and share that I have absence seizures, cerebral palsy, asthma/restrictive thoracic disorder and periodic partial paralysis.",
      answers: [
        {
          answer: "Touch here if I wake up",
          nextQuestion: 7 // hop backwards up to am I moving, so change this from 15? 
        }
      ]
    },





    // heating instructions
    {
      id: 16,
      question: "Jen has periodic partial paralysis. This is normal for Jen. Please help Jen get warmer, especially her hands, neck, and upper legs. It will take some time for Jen to recover; however, once warmer, she will be fully communicative.",
    },

    // cooling instructions
    {
      id: 17,
      question: "Jen has periodic partial paralysis. This is normal for Jen. Please help Jen get cooler, especially her neck, hands, and forehead. Ice or wet paper towels help.  It will take some time for Jen to recover; however, once cooler, she will be fully communicative. ",
    },



    // 911 long - used for breathing response
    {
      id: 18,
      question: "Touch here to call 911. Please share that Jen is having a medical event and has cerebral palsy, asthma/restrictive thoracic disorder, absence seizures, and periodic partial paralysis. ",
      answers: [
        {
          answer: "click here to call 911!",
          nextQuestion: 91111111
        }
      ]
    },



    // 911 final
    {
      id: 19,
      question: "Touch here to call 911. This is not a normal medical event for Jen. Please share that Jen is having a medical event and has cerebral palsy, asthma/restrictive thoracic disorder, absence seizures, and periodic partial paralysis.",
      answers: [
        {
          answer: "click here to call 911!",
          nextQuestion: 91111111
        }
      ]
    }





  ]

  let actionNodes2 = [
    {
      id: 1,
      question: "Please share that Jen might be having a medical event and has recurring health problems.",
      actionNode: 2, // the numbers here are probably useless?
      answers: [
        {
          answer: "Touch here to call 911",
          nextQuestion: 2
        },
      ]
    },
    {
      id: 2,
      question: "Please share that Jen is having a medical event and has cerebral palsy, asthma/restrictive thoracic disorder, absence seizures, and periodic partial paralysis.",
      actionNode: 2,
      answers: [
        {
          answer: "Touch here to call 911",
          nextQuestion: 2
        }
      ]
    },
    {
      id: 3,
      question: "asthma instructions placeholder ",
      answers: [
        {
          answer: "Asthma placeholder button 1",
          actionNode: 4
        },
        {
          answer: "Asthma placeholder button 2",
          actionNode: 5
        }
      ],
      actionNode: 4
    },
    {
      id: 4,
      question: "Jen has periodic partial paralysis. This is normal for Jen. Please help Jen get warmer, especially her hands, neck, and upper legs. It will take some time for Jen to recover; however, once warmer, she will be fully communicative. ",
      actionNode: 5
    },
    {
      id: 5,
      question: "Jen has periodic partial paralysis. This is normal for Jen. Please help Jen get cooler, especially her neck, hands, and forehead. Ice or wet paper towels help.  It will take some time for Jen to recover; however, once cooler, she will be fully communicative.",
      actionNode: 6
    },
    {
      id: 5,
      question: "placeholder seizure timer instructions go here",
      actionNode: 6
    },

    
  ]

  let actionNodes = [
    {
      id: 1,
      question: "Jen has several breathing problems. This is normal for Jen. Please help Jen support the bottom of her ribcage and wait for the timer to end.",
      type: "timed", // should I have 1 for timed 0 for not instead?
      // or perhaps a variable called 
      answers: [
        {
          answer: "click here for next",
          nextQuestion: 2
        }
      ]
    },
    {
      id: 2,
      question: "find ___ for additional help.",
      type: "timed",
      answers: [
        {
          answer: "click here for details",
          nextQuestion: 3
        }
      ]
    }
  ]

  let currentQuestion = questionNodes[0]


  let currentTimerId = 9;


  let tempFunc = () => {
    // if the current question is 12, 18, or 19, then tapping a button should do window.location.href="tel:911"
    if (currentQuestion.id === 12 || currentQuestion.id === 18 || currentQuestion.id === 19) {
      window.location.href = "tel:911"
    }



    currentQuestion = questionNodes[currentQuestion.answers[0].nextQuestion]
    {clearTimeout(currentTimerId)}
    console.log(currentTimerId, " - current timer id")
    console.log('timeoutwas just cleareed with temp func')

    
    }
  



  let qDuringCall = currentQuestion

  let updateQ = () => {
    qDuringCall = currentQuestion
    console.log(qDuringCall, "qDuringCallFunc")
    console.log(currentQuestion, "currentQuestionFunc")
    if (qDuringCall == currentQuestion) {
      console.log(true);
    }
  }



  $: {
    if (currentQuestion.type) {
      console.log("this question has a type")

      {updateQ()}
          {currentTimerId = setTimeout(() => {
            console.log("2 seconds ran")
            if (qDuringCall == currentQuestion) {
              currentQuestion = questionNodes[currentQuestion.answers[0].nextQuestion]
            }
          }, currentQuestion.type * 1000)} 

    }
  }

let firstBtn = "firstBtn"


let isDisabled = false;

$: {
    if (currentQuestion.id === 10) {
      isDisabled = true;
      setTimeout(() => {
        isDisabled = false;
      }, 30000);
    }
  }
</script>





<main>
  <div class="card"> 
    <div class="card-body">
      
      <p class="card-text">{currentQuestion.question}</p>







      <button class="btn-primary" on:click={tempFunc} disabled={isDisabled}>{currentQuestion.answers[0].answer}</button>
      {#if currentQuestion.answers[1]}
        <button class="btn-primary-no" on:click={() => currentQuestion = questionNodes[currentQuestion.answers[1].nextQuestion]}>{currentQuestion.answers[1].answer}</button>
      {/if}
      {#if currentQuestion.answers[2]}
        <button class="btn-primary-no guardian" on:click={() => currentQuestion = questionNodes[currentQuestion.answers[2].nextQuestion]}>{currentQuestion.answers[2].answer}</button>
      {/if}
    </div>
  </div>
</main>







<style>

.card-text {
  white-space: pre-line;
}

.card-body {
  text-align: center;
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.btn-primary {
  background-color: #289642;
  border-color: #28a745;
}

.btn-primary-no {
  background-color: #dc3545;
  border-color: #dc3545;
}

.btn-primary:disabled {
  background-color: #6c757d;
  border-color: #6c757d;
}

</style>

