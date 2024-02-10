<script>
    import { loop_guard } from "svelte/internal";
    import { tweened } from "svelte/motion";
    import shield from './assets/shield.svg';
    import audio from './assets/recording01.mp3';

    


  let questionNodes = [
    {
      id: 0,
      question: "Hi, I'm Jen. I may be having a recurring medical event. Please follow the prompts BEFORE calling 911. Will you help me?",
      answers: [
        {
          answer: "Touch here for Yes",
          nextQuestion: 20
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
          answer: "No", // goto: call 911, with appended bit about Jen having cerebral palsy, asthma, absence seizures, and periodic partial paralysis
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
      question: "This appears not to be a medical emergency for Jen. 911 is not the preferred response. Please Click next to help Jen get her preferred help.",
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
      question: "Try to communicate with Jen and Click next.",
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
          nextQuestion: 22
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
      question: " If Jen is confused, terrified, or otherwise upset, that’s okay. The seizure has passed and Jen is in recovery mode. Jen may not know herself, may be stuttering, or may be entirely nonverbal. Her brain is rebooting. Water, time, and sleep is the only cure. Have her drink some water, and herd her towards one of the wellness rooms once she’s able to stutter in a few minutes. \n\n If Jen is particularly upset, play her some soothing audio.",
      answers: [
        {
          answer: "Click here for audio", // insert link here
          nextQuestion: 3453463 // imgur link to alphabet here?
        },
        {
          answer: "Click here to view the sign language alphabet",
          nextQuestion: 145434234 // this doesn't actually need to lead anywhere, but it will error with this solution(?)
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
          answer: "Click here to call 911!",
          nextQuestion: 91111111
        }
      ]
    },

    {
      id: 20,
      question: "Where are we located?",
      answers: [
        {
          answer: "Columbus, OH",
          nextQuestion: 1
        },
        {
          answer: "Portland, ME",
          nextQuestion: 1
        },
        {
          answer: "Other",
          nextQuestion: 1
        }
      ],
    },

    {
      id: 21,
      question: "I live across the street at the Residence Inn. Room 524. Key should be in my jacket pocket or the pocket of whatever bag I brought today. After a seizure, I’m a little frightened of things in motion, like elevators or cars. Please don’t use my last name or the word home. Please understand that I currently have no filter and will not remember much later. Please be patient.",
      answers: [],
    },
    {
      id: 22,
      question: "Is Jen able to communicate now?",
      answers: [
        {
          answer: "Yes", // goto "talk to jen" ending(?)
          nextQuestion: 23
        },
        {
          answer: "No", // goto goto "contact sneha" call button ending(?) w/ 911 catch if no sneha
          nextQuestion: 24 // 11 was addie but I made a new entry
        }
      ],
    },
    {
      id: 23,
      question: "Talk to Jen!",
      answers: []
    },
    {
      id: 24,
      question: "Ask Sneha for help, telling her that Jen is having asthma related breathing issues.",
      answers: [
        {
          answer: "Tap here to call Sneha."
        }
      ]
    }
  ]

  let currentQuestion = questionNodes[0]


  let currentTimerId = 9;
  const audioElement = new Audio(audio);
  let audioPlaying = false;
  let currentLoc = "other" // "maine" || "ohio"

  // the purpose of this function is to handle the various "final" actions button 1 can perform at the end of the tree
  // I'm sure there has to be a better way to do this. also I'll rename it later
  let primaryButtonHandler = () => {
    
        // this is a bit goofy
        if (currentQuestion.id === 20) {
          currentLoc = "ohio"
          console.log(currentLoc)
        }
        console.log(currentLoc)


    // if the current question is 12, 18, or 19, then tapping a button should dial 911?
    // backQueue.push(currentQuestion.id)
    if (currentQuestion.id === 12 || currentQuestion.id === 18 || currentQuestion.id === 19) {
      window.location.href = "tel:911"
    }

    if (currentQuestion.id === 24) { // might need to get updated when there's more helpers
      window.location.href = "tel:6149159064"
      console.log(questionNodes[24].answers)
      console.log(currentQuestion.id, "24 probs")
      questionNodes[24].answers.push({answer: "didn't work? Try calling 911"})
      questionNodes = questionNodes;
      return;
    }

    



    // if the current question is 13, then play some audio
    if (currentQuestion.id === 13 && !audioPlaying) {
 
        audioPlaying = true;
        // const audioElement = new Audio(audio);
        audioElement.play();

        audioElement.addEventListener('ended', () => {
          audioPlaying = false;
        })
      } else if (audioPlaying && currentQuestion.id === 13) {
        
        console.log(backQueue)
      } else {
        backQueue.push(currentQuestion.id)
        currentQuestion = questionNodes[currentQuestion.answers[0].nextQuestion]
        {clearTimeout(currentTimerId)}
        console.log(currentTimerId, " - current timer id")
        console.log('timeoutwas just cleareed with temp func')

      }

    
    }

    let buttonTwoHandler = () => {
    backQueue.push(currentQuestion.id)  
    // if the current question is 13, then tapping the first button should play/link to audio?
    if (currentQuestion.id === 13) {
      // hard linking to discord, I can fix this later
      window.open("https://cdn.discordapp.com/attachments/1067638884214788176/1128493877179973642/image.png")
      backQueue.pop()
      return;
    }

    if (currentQuestion.id === 24) {
      window.location.href = "tel:6149159064"
      return;
    }

          // this is a bit goofy
          if (currentQuestion.id === 20) {
        currentLoc = "maine"
        console.log(currentLoc)
      }

      currentQuestion = questionNodes[currentQuestion.answers[1].nextQuestion]


    }

    // let buttonThreeHandler = () => {
    // if the current question is 13, then tapping the third button should go to the guardian node instead
    //   currentQuestion = questionNodes[currentQuestion.answers[2].nextQuestion]
    // }

    let guardianHandler = () => {
      backQueue.push(currentQuestion.id)
      const lastId = backQueue[backQueue.length - 1]
      currentQuestion = questionNodes[21]

      console.log(backQueue[backQueue.length - 1], currentLoc)

      if (currentLoc === "ohio") {
        
        if (lastId === 13) { // seizure
          currentQuestion.question = "I live at [description]. Key should be in my jacket pocket or the pocket of whatever bag I brought today. After a seizure, I’m a little frightened of things in motion, like elevators or cars. Please don’t use my last name or the word home. Please understand that I currently have no filter and will not remember much later. Please be patient."
        }
        if (lastId === 23 || lastId === 11) { // asthma, if it's "talk to jen" or "find addie"
          currentQuestion.question = "My breathing machine is located at [description]"
        }

      } else if (currentLoc === "maine") {

      } else { // other "default"
        if (lastId === 13) { // seizure
          currentQuestion.question = "I live at [description]. Key should be in my jacket pocket or the pocket of whatever bag I brought today. After a seizure, I’m a little frightened of things in motion, like elevators or cars. Please don’t use my last name or the word home. Please understand that I currently have no filter and will not remember much later. Please be patient."
        }
        if (lastId === 23) { // asthma
          currentQuestion.question = "My breathing machine is located in my carry-on suitcase. Thank you!"
        }

      }

      // () => currentQuestion.question = currentQuestion.guardianText
      // currentQuestion.answers = [] // manually clearing the array here might not be the best option?
      // currentQuestion.question = "I live across the street at the Residence Inn. Room 524. Key should be in my jacket pocket or the pocket of whatever bag I brought today. My breathing machine is in the cooler bag on the right bedside table. Thank you!"
    }

    let backQueue = [];

    let backHandler = () => {
      isDisabled = false;

      const lastId = backQueue.pop()
      const newQ = questionNodes.filter((question) => lastId === question.id)
      currentQuestion = newQ[0]
      console.log(currentQuestion)

      if (audioPlaying) {
        audioElement.pause()
        audioPlaying = false;
        audioElement.currentTime = 0;
      }
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
          }, currentQuestion.type * 1000)} // make sure to fix the multiplier later! this one is for moving forward, not disabling

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


      <!-- this is a mess of ifs I'll refactor later -->
      {#if (!currentQuestion.timerAnim || Math.trunc($timeVar) <= 0) && currentQuestion.answers.length > 0}
        <button class="btn-primary" on:click={primaryButtonHandler} disabled={isDisabled}>{currentQuestion.answers[0].answer}</button>
      {/if}
      {#if currentQuestion.answers[1]}
        {#if currentQuestion.id === 13}
          <p class="card-text">Does Jen look confused at communication attempts?</p>
        {/if}
        <button class={currentQuestion.id === 20 ? "btn-primary" : "btn-primary-no"} on:click={buttonTwoHandler}>{currentQuestion.answers[1].answer}</button>
      {/if}
      {#if currentQuestion.answers[2]}
        <button class={currentQuestion.id === 20 ? "btn-primary" : "btn-primary-no guardian"} on:click={() => currentQuestion = questionNodes[currentQuestion.answers[2].nextQuestion]}>{currentQuestion.answers[2].answer}</button>
      {/if}
      {#if currentQuestion.answers.length === 0 || currentQuestion.id === 13}
        <button class="guardian-btn" on:click={guardianHandler}>
          <img alt="guardian shield logo" src={shield} />
        </button>
      {/if}

      {#if currentQuestion.id > 0} 
        <button class="back" on:click={backHandler}>←Back</button>
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
  bottom: -50px;
  left: -10px;
  background-color: transparent;
}

img {
  width: 70px;
  opacity: 0.12;
}

.back {
  border: none;
  background: none;
  margin: 0;
  padding: 0;
  text-align: left;
  text-decoration: underline;
  color: rgb(255, 38, 0);
  font-weight: bold;
}

</style>