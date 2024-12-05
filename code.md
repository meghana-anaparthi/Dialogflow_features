# Custom payload for feedback component

{
  "richContent": [
    [
      {
        "payload": {},
        "name": "thumbs-feedback",
        "type": "custom_template"
      }
    ]
  ]
}

# Thumbs up and Thumbs down buttons:

//Thumbs up and Thumbs down buttons
class ThumbsFeedback extends HTMLElement {
  constructor() {
    super();
    this.dfPayload = null; // Payload from Dialogflow
    this.dfResponseId = null; // Dialogflow response ID
    this.renderRoot = this.attachShadow({ mode: "open" });
    this.countUp = 0; // Track thumbs-up clicks
    this.countDown = 0; // Track thumbs-down clicks
  }

  connectedCallback() {
    // Create the chat bubble container
    const bubbleContainer = document.createElement("div");
    bubbleContainer.style.display = "flex";
    bubbleContainer.style.justifyContent = "flex-start";
    bubbleContainer.style.margin = "10px 20px";

    // Create the chat bubble
    const bubble = document.createElement("div");
    bubble.style.backgroundColor = "#FFFFFF"; // White background for the bubble
    bubble.style.borderRadius = "12px";
    bubble.style.padding = "10px 15px";
    bubble.style.maxWidth = "300px";
    bubble.style.boxShadow = "0 2px 4px rgba(0, 0, 0, 0.1)";
    bubble.style.display = "flex";
    bubble.style.justifyContent = "center";
    bubble.style.alignItems = "center";
    bubble.style.gap = "15px";

    // Create Thumbs Up Button
    const thumbsUpButton = document.createElement("button");
    thumbsUpButton.innerHTML = `
      <img src="./Thumbs_up.png" alt="Thumbs Up" width="30" height="30" />
    `;
    thumbsUpButton.style.border = "none";
    thumbsUpButton.style.backgroundColor = "transparent";
    thumbsUpButton.style.cursor = "pointer";
    thumbsUpButton.style.padding = "0";

    // Event Handler for Thumbs Up
    thumbsUpButton.onclick = () => {
      if (this.countUp < 1) {
        this.countUp += 1;

        const dfMessenger = document.querySelector("df-messenger");
        // Update the session parameter with the TU_feedback value
    
        const queryParameters = {
          parameters: {
            feedback_TU: "Positive", // add the positive feedback value
          },
        };

          // Set the updated query parameters
          dfMessenger.setQueryParameters(queryParameters);
        
        dfMessenger.renderCustomText("Thank you for your feedback!", true);

        console.log("Thumbs Up clicked and feedback sent.");
      }
    };

    bubble.appendChild(thumbsUpButton);

    // Create Thumbs Down Button
    const thumbsDownButton = document.createElement("button");
    thumbsDownButton.innerHTML = `
      <img src="./Thumbs_down.png" alt="Thumbs Down" width="30" height="30" />
    `;
    thumbsDownButton.style.border = "none";
    thumbsDownButton.style.backgroundColor = "transparent";
    thumbsDownButton.style.cursor = "pointer";
    thumbsDownButton.style.padding = "0";

    // Event Handler for Thumbs Down
    thumbsDownButton.onclick = () => {
      if (this.countDown < 1) {
        this.countDown += 1;

        const dfMessenger = document.querySelector("df-messenger");
        const payload = [
          {
            type: "custom_template",
            name: "feedback-template",
            payload: {
              title: "Why did you choose this rating? (optional)",
              options: ["Irrelevant", "Incorrect", "Unsafe"],
              placeholder: "Additional Feedback",
              submitButtonText: "Submit"
            }
          }
        ];
        dfMessenger.renderCustomCard(payload);

        console.log("Thumbs Down clicked and feedback template rendered.");
      }
    };

    bubble.appendChild(thumbsDownButton);

    bubbleContainer.appendChild(bubble);
    this.renderRoot.appendChild(bubbleContainer);
  }
}

// Define the custom element
customElements.define("thumbs-feedback", ThumbsFeedback);



// Feedback capture

    class FeedbackTemplate extends HTMLElement {
  constructor() {
    super();
    this.dfPayload = null;
    this.dfResponseId = null;
    this.renderRoot = this.attachShadow({ mode: "open" });
    this.selectedOptions = []; // Array to store selected options
  }

  connectedCallback() {
    // Create container
    const container = document.createElement("div");
    container.style.border = "1px solid #ddd";
    container.style.borderRadius = "10px";
    container.style.padding = "15px";
    container.style.width = "90%";
    container.style.margin = "10px auto";
    container.style.boxShadow = "0 4px 6px rgba(0, 0, 0, 0.1)";
    container.style.backgroundColor = "#fff";
    container.style.fontFamily = "'Arial', sans-serif";
    container.style.textAlign = "center";

    // Title
    const title = document.createElement("h4");
    title.innerText = this.dfPayload.title;
    title.style.marginBottom = "10px";
    title.style.fontSize = "16px";
    title.style.color = "#333";
    container.appendChild(title);

    // Subtitle (optional)
    const subtitle = document.createElement("span");
    subtitle.innerText = "(optional)";
    subtitle.style.fontSize = "12px";
    subtitle.style.color = "#666";
    subtitle.style.display = "block";
    subtitle.style.marginBottom = "10px";
    container.appendChild(subtitle);

    // Option Buttons
    const optionsContainer = document.createElement("div");
    optionsContainer.style.marginBottom = "15px";
    optionsContainer.style.display = "flex";
    optionsContainer.style.flexWrap = "wrap";
    optionsContainer.style.justifyContent = "center";

    this.dfPayload.options.forEach(option => {
      const button = document.createElement("button");
      button.innerText = option;
      button.style.margin = "5px";
      button.style.padding = "8px 12px";
      button.style.border = "1px solid #ddd";
      button.style.borderRadius = "5px";
      button.style.cursor = "pointer";
      button.style.backgroundColor = "#f7f7f7";
      button.style.color = "#333";
      button.style.fontSize = "14px";

      // Toggle option selection on click
      button.onclick = () => {
        if (this.selectedOptions.includes(option)) {
          this.selectedOptions = this.selectedOptions.filter(
            selected => selected !== option
          );
          button.style.backgroundColor = "#f7f7f7"; // Reset button style
          button.style.color = "#333";
        } else {
          this.selectedOptions.push(option);
          button.style.backgroundColor = "#007BFF"; // Highlight selected option
          button.style.color = "#fff";
        }
        toggleSubmitButton(); // Check if submit should be enabled
      };
      optionsContainer.appendChild(button);
    });
    container.appendChild(optionsContainer);

    // Input Field
    const input = document.createElement("textarea");
    input.placeholder = this.dfPayload.placeholder;
    input.style.display = "block";
    input.style.margin = "10px auto";
    input.style.width = "90%";
    input.style.height = "50px";
    input.style.padding = "10px";
    input.style.border = "1px solid #ddd";
    input.style.borderRadius = "5px";
    input.style.fontSize = "14px";
    input.style.resize = "none";

    // Enable submit button on input
    input.addEventListener("input", () => toggleSubmitButton());
    container.appendChild(input);

    // Instruction Text
    const instruction = document.createElement("p");
    instruction.innerText = "Please do not provide any personal or sensitive data";
    instruction.style.fontSize = "12px";
    instruction.style.color = "#666";
    instruction.style.marginTop = "5px";
    instruction.style.marginBottom = "10px";
    container.appendChild(instruction);

    // Submit Button
    const submitButton = document.createElement("button");
    submitButton.innerText = this.dfPayload.submitButtonText;
    submitButton.style.marginTop = "10px";
    submitButton.style.padding = "10px 20px";
    submitButton.style.border = "none";
    submitButton.style.borderRadius = "5px";
    submitButton.style.cursor = "pointer";
    submitButton.style.fontSize = "14px";
    submitButton.style.backgroundColor = "#007BFF";
    submitButton.style.color = "#fff";
    submitButton.style.boxShadow = "0 4px 6px rgba(0, 0, 0, 0.1)";
    submitButton.disabled = true; // Initially disable the button
    submitButton.style.opacity = "0.5";


    // Handle submit button click
    submitButton.onclick = () => {
      const feedback = input.value.trim();
      const submission = {
        selectedOptions: this.selectedOptions,
        feedback: feedback
      };
      // Update the session parameter with the feedback value
    const dfMessenger = document.querySelector("df-messenger");
    const queryParameters = {
      parameters: {
        feedback_TD: "Negative",
        feedback_info: JSON.stringify(submission), // add the feedback info value
      },
    };

    // Set the updated query parameters
    dfMessenger.setQueryParameters(queryParameters);

    dfMessenger.renderCustomText("Thank you for your feedback!", true);

    // Log the updated linkInfo for debugging
    console.log("Updated feedback_info parameter:", JSON.stringify(submission));
  }
  //alert(`Submission logged: ${JSON.stringify(submission)}`);

      
    container.appendChild(submitButton);

    this.renderRoot.appendChild(container);

    // Function to enable/disable submit button
    const toggleSubmitButton = () => {
      const feedback = input.value.trim();
      if (this.selectedOptions.length > 0 || feedback) {
        submitButton.disabled = false; // Enable submit if conditions met
        submitButton.style.opacity = "1";
      } else {
        submitButton.disabled = true; // Disable submit otherwise
        submitButton.style.opacity = "0.5";
      }
    };
  }
}

customElements.define("feedback-template", FeedbackTemplate);

