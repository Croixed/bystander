<script>
    import { loop_guard } from "svelte/internal";
    import { tweened } from "svelte/motion";
    import shield from './assets/shield.svg'

  let questionNodes = [
    {
      id: 0,
      question: "Hi, I'm Jen. I may be having a recurring medical event. Please follow the prompts BEFORE calling 911. Will you help me?",
      answers: [
        {
          answer: "Touch here for Yes",
          nextQuestion: 1
        },
        {
          answer: " Touch here for No", // goto: call 911
          nextQuestion: 12
        }
      ]
    },
    {
      question: "Is Jen breathing?",
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
      question: "This appears not to be a medical emergency for Jen. 911 is not the preferred response. Please click next to help Jen get her preferred help.",
      id: 3,
      answers: [
        {
          answer: "Next",
          nextQuestion: 4
        }
      ]
    },




    {
      question: "Is Jen conscious/awake?",
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
      question: "Is Jen coherent/making sense?",
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
      question: "Try to communicate with Jen and click next.",
      answers: [
        {
          answer: "Next",
          nextQuestion: 8
        }
      ]
    },











    {
      question: "Is Jen moving?",
      id: 7,
      answers: [
        {
          answer: "Yes",
          nextQuestion: 14 // seizure instructions (14 goes to the warning first, then 13 is the actual instructs)
        },
        {
          answer: "No",
          nextQuestion: 8 /// am I cold?
        }
      ]

    },
    {
      question: "Does Jen seem cold?",
      id: 8,
      answers: [
        {
          answer: "No",
          nextQuestion: 9
        },
        {
          answer: "Yes",
          nextQuestion: 16
        }
      ]
    },
    {
      question: "Does Jen seem warm?",
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
      // type: 240,
      timerAnim: 5,
      answers: [
        {
          answer: "Touch here for next",
          nextQuestion: 11
        }
      ]
    },
    {
      id: 11,
      question: "find Addie for additional help.",
      guardian: true,
      guardianText: "I live across the street at the Residence Inn. Room 524. Key should be in my jacket pocket or the pocket of whatever bag I brought today. My breathing machine is in the cooler bag on the right bedside table. Thank you!",
      answers: [
        // {
        //   answer: "Touch here for details",
        //   nextQuestion: 2000 // update this to later lead to her address info, and later have pin functionality? 
        // }
      ]
    },
    {
      id: 12,
      question: "Please share that Jen might be having a medical event and has recurring health problems. ",
      answers: [
        {
          answer: "Touch here to call 911",
          nextQuestion: 91111111
        }
      ]

    },






    // 13 and 14 are seizure non timer instructions
    {
      id: 13,
      question: " If Jen is confused, terrified, or otherwise upset, that’s okay. The seizure has passed and Jen is in recovery mode. Jen may not know herself, may be stuttering, or may be entirely nonverbal. Her brain is rebooting. Water, time, and sleep is the only cure. Have her drink some water, and herd her towards one of the wellness rooms once she’s able to stutter in a few minutes. \n\n If jen is particularly upset, play her some soothing audio.",
      answers: [
        {
          answer: "Click here for audio", // insert link here
        },
        {
          answer: "Click here to view the sign language alphabet",
          nextQuestion: 14
        },
      ]

    },
    {
      // this was the original guardian instructions but I'm changing it to be the warning before the big text
      id: 14,
      question: "Everything’s fine. Jen has atypical absence seizures. Do not call 911 unless she’s convulsing (not normal) or is visibly bleeding. This is normal for Jen. Please remain calm. Being comforting as you might to a child is helpful.",
      answers: [
        {
          answer: "Next",
          nextQuestion: 13
        }
      ]
    },






    // seizure timer instructions. should technically comme before 12 and 13 but I'll fix it later(?)
    {
      id: 15,
      type: 60, // I'll probably change type to "timerMinutes" later instead
      question: "Please wait one minute. A link to call 911 will automatically appear after one minute. If Jen does not regain consciousness, or is screaming while unconscious, please call 911 and share that Jen has absence seizures, cerebral palsy, asthma/restrictive thoracic disorder and periodic partial paralysis.",
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
      question: "This is normal for Jen. Please help Jen get warmer, especially her hands, neck, and upper legs.It will take some time for Jen to recover; however, once warmer, she will be fully communicative. ",
      answers: []
    },

    // cooling instructions
    {
      id: 17,
      question: "Jen has periodic partial paralysis. This is normal for Jen. Please help Jen get cooler, especially her neck, hands, and forehead. Ice or wet paper towels help.  It will take some time for Jen to recover; however, once cooler, she will be fully communicative. ",
      answers: []
    },



    // 911 long - used for breathing response
    {
      id: 18,
      question: "Please share that Jen is having a medical event and has cerebral palsy, asthma/restrictive thoracic disorder, absence seizures, and periodic partial paralysis. ",
      answers: [
        {
          answer: "Touch here to call 911",
          nextQuestion: 91111111 // update this later to make it link
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

  let currentQuestion = questionNodes[0]


  let currentTimerId = 9;

  // I'll rename this later
  let tempFunc = () => {
    // if the current question is 12, 18, or 19, then tapping a button should dial 911
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
    if (currentQuestion.id === 15) {
      console.log("this question has a type")

      {updateQ()}
          {currentTimerId = setTimeout(() => {
            console.log("2 seconds ran")
            if (qDuringCall == currentQuestion) {
              currentQuestion = questionNodes[12]
            }
          }, currentQuestion.type * 1000)} // make sure to fix the multiplier later!!!! !!!! this one is for moving forward, not disabling

    }
  }

let firstBtn = "firstBtn"


let isDisabled = false;

$: {
    if (currentQuestion.id === 10) {
      isDisabled = true;
      setTimeout(() => {
        isDisabled = false;
      }, 300000);
    }
  }



  let orig = 300
  let timeVar = tweened(orig)

  setInterval(()=> {
    if ($timeVar >= 1 && currentQuestion.timerAnim) {
      $timeVar--
      console.log($timeVar)
    }
  }, 1000)

</script>





<main>
  <div class="card"> 
    <div class="card-body">
      
      <p class="card-text">{currentQuestion.question}</p>



      {#if currentQuestion.timerAnim}
        <div class="timerAnim">Next will appear at the end of the timer: <br> {Math.round($timeVar)} seconds remaining </div>
      {/if}


      <!-- this is getting a little messy and I'll refactor later -->
      {#if (!currentQuestion.timerAnim || Math.trunc($timeVar) <= 0) && currentQuestion.answers.length > 0}
        <button class="btn-primary" on:click={tempFunc} disabled={isDisabled}>{currentQuestion.answers[0].answer}</button>
      {/if}
      {#if currentQuestion.answers[1]}
        {#if currentQuestion.id === 13}
          <p class="card-text">Does Jen look confused at communication attempts?</p>
        {/if}
        <button class="btn-primary-no" on:click={() => currentQuestion = questionNodes[currentQuestion.answers[1].nextQuestion]}>{currentQuestion.answers[1].answer}</button>
      {/if}
      {#if currentQuestion.answers[2]}
        <button class="btn-primary-no guardian" on:click={() => currentQuestion = questionNodes[currentQuestion.answers[2].nextQuestion]}>{currentQuestion.answers[2].answer}</button>
      {/if}
      {#if currentQuestion.answers.length === 0 || currentQuestion.id === 13}
        <button class="guardian-btn" on:click={() => currentQuestion.question = currentQuestion.guardianText}>
          <img alt="guardian shield logo" src={shield} />
        </button>
      {/if}
    </div>
  </div>
</main>







<style>

.card-text {
  white-space: pre-line;
  max-width: 300px;
  margin: 0 auto;
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


.guardian-btn {
  position: absolute;
  bottom: -10px;
  left: -10px;
  background-color: transparent;
}

img {
  width: 70px;
  opacity: 0.12;
}

</style>

