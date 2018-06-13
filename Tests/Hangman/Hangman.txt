let dictionary = [
	"AIRPLANE", 
  "PASSWORD",
  "APPLE",
  "MONKEY",
  "HELICOPTER",
  "HANGMAN",
  "ELEPHANT",
  "COMPUTER"
];

const Word = (props) => {
	let word = props.word;
  let usedLetters = props.usedLetters;
  
	return (
  	<div className="text-center col-8">
    	<h6>Word</h6>
      {
      	word.split('').map((letter, i) => 
        	<span key={'Word'+i} 
          			className="word">
          	{usedLetters.indexOf(letter) >= 0 ? letter : '_'}
          </span>
        )
      }
    </div>
  );
}

const Result = (props) => {
	let errors = props.errors;
  let imageName = "Hangman" + errors + ".png";
  
	return (
  	<div className="col-4 text-center">
    	<h6>Result</h6>
      <img width="100" 
      		 src={"https://juancrud.github.io/React/Tests/Hangman/images/" + imageName} 
           alt="Hangman"/>
    </div>
  );
}

const Letters = (props) => {
	let usedLetters = props.usedLetters;
  let selectLetter = props.selectLetter;
  
	return (
  	<div className="text-center col-12">
    	<h4>Letters</h4>
      {
      	Letters.allLetters.map((letter, i) => 
        	<span key={'Letter'+letter} 
          			className={usedLetters.indexOf(letter) >= 0 ? 'letter used' : 'letter'}
                onClick={() => selectLetter(letter)}>
          	{letter}
          </span>
        )
      }
    </div>
  );
}
Letters.allLetters = 'ABCDEFGHIJKLMNÑOPQRSTUVWXYZ'.split('');

const Status = (props) => {
	let statusId = props.statusId;
  let playAgain = props.playAgain;
  
	return (
  	<div className="text-center col-12">
    	<h5>{statusId === StatusEnum.Won ? "Yeah, you won!" : "Game Over!"}</h5>
      <button className="btn primary" onClick={() => playAgain()}>Play Again</button>
    </div>
  );
}

class StatusEnum {
	static Init = 0;
  static InProgress = 1;
  static Won = 2;
  static Lost = 3;
};

class HangmanGame extends React.Component {
	static initialState = (word) => ({
  	word: word,
    usedLetters: [],
    errors: 0,
    statusId: StatusEnum.Init
  });
  
  getNewWord = () => {
  	let idx = Math.floor(Math.random() * dictionary.length);
  	return dictionary[idx];
  };
  
  startGame = () => {
  	this.setState(HangmanGame.initialState(this.getNewWord()));
  };
  
  checkStatus = () => {
  	this.setState(prevState => {
    	if (prevState.word.split('').every(x => prevState.usedLetters.indexOf(x) >= 0))
      	return { statusId: StatusEnum.Won };
        
      if (prevState.errors === 6)
      	return { statusId: StatusEnum.Lost };
        
      return { statusId: StatusEnum.InProgress };
    });
  };
  
  selectLetter = (letter) => {
  	if (this.state.statusId === StatusEnum.Won) return;
    if (this.state.statusId === StatusEnum.Lost) return;
    
  	this.setState(prevState => ({
    	errors: prevState.word.indexOf(letter) >= 0 ? prevState.errors : prevState.errors + 1,
    	usedLetters: prevState.usedLetters.concat(letter),
    }), this.checkStatus);
  };
  
  state = HangmanGame.initialState(this.getNewWord());
	render() {
  	return (
    	<div className="container">
    	  <h3 className="text-center">Hangman</h3>
        <hr />
        <div className="row">
          <Word word={this.state.word} usedLetters={this.state.usedLetters}></Word>
          <Result errors={this.state.errors}></Result>
        </div>
        {
        	this.state.statusId === StatusEnum.Won || this.state.statusId === StatusEnum.Lost ?
          	<div className="row mt-4">
            	<Status statusId={this.state.statusId} playAgain={this.startGame}></Status>
            </div>
            :
            <div className="row mt-4">
            	<Letters usedLetters={this.state.usedLetters} selectLetter={this.selectLetter}></Letters>
            </div>
        }
    	</div>
    )
  }
}

class App extends React.Component {
	render() {
  	return (
    	<div>
      	<HangmanGame></HangmanGame>
    	</div>
    );
  }
}

ReactDOM.render(<App />, mountNode);
