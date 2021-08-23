

# Scrooge Coin, (Coursera Bitcoin)
  [![CMake](https://github.com/mattcoding4days/extras/actions/workflows/cmake.yml/badge.svg?branch=dev)](https://github.com/mattcoding4days/extras/actions/workflows/cmake.yml)

> This repo is based on both the ScroogeCoin programming assignment practice of "Bitcoin and Cryptocurrency Technologies" online course as well as the work completedby [Huabing Zhao](https://medium.com/@zhaohuabing/scrooge-coin-c1d1d1e9fd00) . The course [_“Bitcoin and Cryptocurrency Technologies”_](https://www.coursera.org/learn/cryptocurrency/home/welcome) has three assignments designed to get the student ramped up quickly as to the many facits of the subject matter. However, as Mr. Zhao was quick to point out, there were some implementation details that weren't explained to well, (perhaps to give the student a lesson in learning those details).

*"This amazing course is created by the professors of Princeton University. It's free on the internet. The course has a series of well-organized lecture videos and programming practices after each lecture. I encourage anyone who is interested in cryptocurrency to attend this travis-ci onlie course. You will get a chance to learn the theories and technical details behind the popular Bitcoin and may even create your own version of cryptocurrency after finishing this course!*
-- Huabing Zhao

 - **GIVEN** there is a lot of material to cover in such a short amount of time
 - **WHEN** we  group what we understand as the behaviour of Bitcoin and Cryptocurrency
 - **THEN** we can places specific test cases and interfaces in this Github project
 

The original problem statement reads as follows:

> You will be responsible for creating a file called **TxHandler.java** that implements the following API:
> 
	public class TxHandler {

	  /** Creates a public ledger whose current UTXOPool 
	    * (collection of unspent transaction outputs) is utxoPool. 
	    * This should  make a defensive copy of utxoPool by using 
	    * the UTXOPool (UTXOPool uPool) constructor.
	    */
	  public TxHandler(UTXOPool utxoPool);

	  /** Returns true if
	   * (1) all outputs claimed by tx are in the current UTXO pool
	   * (2) the signatures on each input of tx are valid
	   * (3) no UTXO is claimed multiple times by tx
	   * (4) all of tx’s output values are non-negative
	   * (5) the sum of tx’s input values is greater than or equal 
	   * to the sum of its output values; and false otherwise.
	   */
	  public boolean isValidTx(Transaction tx);

	  /** Handles each epoch by receiving an unordered array of 
	   * proposed transactions, checking each transaction for 
	   * correctness, returning a mutually valid array of accepted 
	   * transactions, and updating the current UTXO pool as 
	   * appropriate.
	   */
	  public Transaction[] handleTxs(Transaction[] possibleTxs);

	}
	
> Your implementation of handleTxs() should return a mutually valid transaction set of maximal size (one that can’t be enlarged simply by adding more transactions). It need not compute a set of maximum size (one for which there is no larger mutually valid transaction set).

> Based on the transactions it has chosen to accept, handleTxs() should also update its internal UTXOPool to reflect the current set of unspent transaction outputs, so that future calls to handleTxs() and isValidTx() are able to correctly process/validate transactions that claim outputs from transactions that were accepted in a previous call to handleTxs().

> Extra Credit: Create a second file called MaxFeeTxHandler.java whose handleTxs() method finds a set of transactions with maximum total transaction fees -- i.e. maximize the sum over all transactions in the set of (sum of input values - sum of output values)).
  
> Course url: https://www.coursera.org/learn/cryptocurrency
> Blog: https://zhaohuabing.com/category/note/

## Interfaces
The project did not come with any properly defined interfaces as such, (*albeit, the problem statement did define a neat class for us to start with*). However, the purpose of doing this course is not so much to be a good student as it is to come to know how to use Bitcoin and Cryptocurrency technology in general, (*albeit the sooner the better)*. As such, the first thing we look for is what the code is actually trying to do and how it is meant to be used. Hence, we define our own interfaces.
## Interfaces in general
In the Jason Borne trilogy of movies, we find the protagonist, (or antagonist) inside the American embassy and looking for the exit, (*as he did not have much in the way of time at his disposal*). Upon seeing the fire escape plans plainly on the wall, he immediately knew the layout of the building and it's many floors. This is what a well defined **interface** does for software. A well defined interface lets an experienced software developer quickly understand the layout of a given piece of software without having to inspect every single aspect of the current implementation. So, the first thing we want to do when faced with any new technology is identify the interfaces, (or *plans for the escape route*) if they exist at all. 

Here is the initial interface for the transaction class, (that we managed to extract from one of the supplied classes for the project):

	/**
	 * @brief TransactionInterface
	 * 
	 */
	interface TransactionInterface extends CoinDistributerInterface {

	    public byte[] getRawDataToSign(int index);
	    public void addSignature(byte[] signature, int index);
	    public byte[] getRawTx();
	    public void finalize();
	    public void setHash(byte[] h);
	    public byte[] getHash();
	    public void removeInput(int index);
	    public void removeInput(UTXO ut);
	    public ArrayList<InputInterface> getInputs();
	    public ArrayList<OutputInterface> getOutputs();
	    public InputInterface getInput(int index);
	    public OutputInterface getOutput(int index);
	    public int numInputs();
	    public int numOutputs();

	}

  (**Note:** recent updates to source code may differ from the code listed here)

  Here is part of that interface that we drew into it's own interface:

	interface CoinDistributerInterface {

	    /**
	     * Every transaction has a set of inputs and a set of outputs.
	     * 
	     * An input in a transaction must use a hash pointer to refer to its
	     * corresponding output in the previous transaction, and it must be signed with
	     * the private key of the owner because the owner needs to prove he/she agrees
	     * to spend his/her coins.
	     * 
	     * @param prevTxHash
	     * @param outputIndex
	     */
	    @Deprecated
	    public void addInput(byte[] prevTxHash, int outputIndex);

	    /**
	     * Every transaction has a set of inputs and a set of outputs.
	     * 
	     * Every output is correlated to the public key of the receiver, which is
	     * his/her ScroogeCoin address.
	     * 
	     * @param value
	     * @param address
	     */

	    @Deprecated
	    public void addOutput(double value, PublicKey address);

	}

Here is the current implementation of what we feel is a much better representation of the functionality used in this project:

	interface CoinAuthorityInterface {
	
	    public CoinCreatorInterface getCreator();
	    
	    public TransactionInterface addCoinForSale(
		    TransactionInterface tx, 
		    TransactionInterface source, 
		    int index);
		    
	    public TransactionInterface addBuyer(
		    TransactionInterface tx, 
		    double amount, 
		    CoinOwnerInterface buyer);
		    
	    public TransactionInterface authorizeSale(
		    TransactionInterface tx, 
		    CoinOwnerInterface seller, 
		    int index)
	            throws NoSuchAlgorithmException, NoSuchProviderException, 
	            SignatureException, InvalidKeyException;

	}

	interface CoinOwnerInterface {
	    public PublicKey getPublicKey();
	    public PrivateKey getPrivateKey();
	}

	interface CoinCreatorInterface extends CoinOwnerInterface {
	    public TransactionInterface createCoin(double value);
	}
	
Before our interface definitions the test cases used to interact with the requested **handleTxs** looked like this:

        /**
         * @brief Scrooge has right to create coins
         * 
         *        In the first transaction, we assume that Scrooge has created 10 coins
         *        and assigned them to himself, we don’t doubt that because the
         *        system-Scroogecoin has a building rule which says that Scrooge has
         *        right to create coins.
         * 
         */

        // Here: Scrooge transfers 4 coins to Alice, 6 coins to bob, no transaction fee

		Transaction tx = new Transaction();
		tx.addInput(genesiseTx.getHash(), 0);
		tx.addOutput(4, aliceKeypair.getPublic());
		tx.addOutput(7, bobKeypair.getPublic());
		byte[] sig = signMessage(scroogeKeypair.getPrivate(), tx.getRawDataToSign(0));
		tx.addSignature(sig, 0);
		tx.finalize();
		assertFalse(txHandler.isValidTx(tx));
		
After we refactored and applied our interfaces, the test cases are transformed to this:

        TransactionInterface tx = new Transaction();
        tx = authority.addCoinForSale(tx, genesiseTx, 0);
        tx = authority.addBuyer(tx, 4, Alice);
        tx = authority.addBuyer(tx, 6, Bob);
        tx = authority.authorizeSale(tx, Scrooge, 0);

With properly written interfaces the code reads much more naturally.

Further, a proper interface for the transaction handler itself, was given a nice overhaul:

	 
	 interface TxHandlerInterface {
	
		/**
		 * @brief isValidTx()
		 * 
		 *        Should the input value and output value be equal? Otherwise the ledger
		 *        will become unbalanced.
		 * 
		 *        true if:
		 * 
		 * @implNote (1) all outputs claimed by {@code tx} are in the current UTXO pool,
		 * @implNote (2) the signatures on each input of {@code tx} are valid,
		 * @implNote (3) no UTXO is claimed multiple times by {@code tx},
		 * @implNote (4) all of {@code tx}s output values are non-negative, and
		 * @implNote (5) the sum of {@code tx}s input values is greater than or equal to
		 *           the sum of its output values; and false otherwise.
		 * 
		 * @throws ConsumedCoinAvailableException,
		 * @throws VerifySignatureOfConsumeCoinException,
		 * @throws CoinConsumedMultipleTimesException,
		 * @throws TransactionOutputLessThanZeroException,
		 * @throws TransactionInputSumLessThanOutputSumException
		 * 
		 */	    
		 
		 public boolean isValidTx(TransactionInterface tx) throws ConsumedCoinAvailableException,
	            VerifySignatureOfConsumeCoinException, CoinConsumedMultipleTimesException,
	            TransactionOutputLessThanZeroException, TransactionInputSumLessThanOutputSumException;

		/**
		 * Handles each epoch by receiving an unordered array of proposed transactions,
		 * checking each transaction for correctness, returning a mutually valid array
		 * of accepted transactions, and updating the current UTXO pool as appropriate.
		 */
		 
	    public TransactionInterface[] handleTxs(TransactionInterface[] possibleTxs) throws Exception;
	    
	}

## Custom Exceptions
The introduction of exceptions to C++, (and then other languages) brought with it a much better way of dealing with real world events such as null pointers, (and the occasional car crash). Before the advent of exceptions, over half of the programmers time was consumed in error handling. Further and more often than not, the error handling solutions would require more work than made the initial software practical. These were the days of COBOL and GW BASIC. Since the inception of the throw/try/catch block however, life became much simpler for the experienced software developer. In short, if the unintended happens, throw an exception and the language itself will handle the releasing of resources, (all the way back to the nearest catch block). 

Custom exceptions then were like the icing on the cake. Being able to organize possible exceptions as a well defined class made it much easier to expect the unexpected, (*and recover from it gracefully*).

Case in point. When Mr. Huabing wrote his code, he places **if** statements inside his handler:

	/**
	 * @return true if: (1) all outputs claimed by {@code tx} are in the current
	 *         UTXO pool, (2) the signatures on each input of {@code tx} are valid,
	 *         (3) no UTXO is claimed multiple times by {@code tx}, (4) all of
	 *         {@code tx}s output values are non-negative, and (5) the sum of
	 *         {@code tx}s input values is greater than or equal to the sum of its
	 *         output values; and false otherwise. //Should the input value and
	 *         output value be equal? Otherwise the ledger will become unbalanced.
	 */
	public boolean isValidTx(Transaction tx) {
		Set<UTXO> claimedUTXO = new HashSet<UTXO>();
		double inputSum = 0;
		double outputSum = 0;

		List<Transaction.Input> inputs = tx.getInputs();
		for (int i = 0; i < inputs.size(); i++) {
			Transaction.Input input = inputs.get(i);

			if (!isConsumedCoinAvailable(input)) {
				return false;
			}

			if (!verifySignatureOfConsumeCoin(tx, i, input)) {
				return false;
			}

			if (isCoinConsumedMultipleTimes(claimedUTXO, input)) {
				return false;
			}

			UTXO utxo = new UTXO(input.prevTxHash, input.outputIndex);
			Transaction.Output correspondingOutput = utxoPool.getTxOutput(utxo);
			inputSum += correspondingOutput.value;

		}

		List<Transaction.Output> outputs = tx.getOutputs();
		for (int i = 0; i < outputs.size(); i++) {
			Transaction.Output output = outputs.get(i);
			if (output.value <= 0) {
				return false;
			}

			outputSum += output.value;
		}

		// Should the input value and output value be equal? Otherwise the ledger will
		// become unbalanced.
		// The difference between inputSum and outputSum is the transaction fee
		if (outputSum > inputSum) {
			return false;
		}

		return true;
	}

In this current situation, there is no need to do anything special if any of the **if** are not true, (requiring the function to return a false). However, in a real world situation having **if** statements inside the principle area of functionality is a recipe for complications. The less error prone way of doing this is converting all those if statements into custom exception assertions:

Look at how much simpler the resulting **production** code looks now:

	/**
	 * @brief isValidTx()
	 * 
	 *        Should the input value and output value be equal? Otherwise the ledger
	 *        will become unbalanced.
	 * 
	 *        true if:
	 * 
	 * @implNote (1) all outputs claimed by {@code tx} are in the current UTXO pool,
	 * @implNote (2) the signatures on each input of {@code tx} are valid,
	 * @implNote (3) no UTXO is claimed multiple times by {@code tx},
	 * @implNote (4) all of {@code tx}s output values are non-negative, and
	 * @implNote (5) the sum of {@code tx}s input values is greater than or equal to
	 *           the sum of its output values; and false otherwise.
	 * 
	 * @throws ConsumedCoinAvailableException,
	 * @throws VerifySignatureOfConsumeCoinException,
	 * @throws CoinConsumedMultipleTimesException,
	 * @throws TransactionOutputLessThanZeroException,
	 * @throws TransactionInputSumLessThanOutputSumException
	 * 
	 */
	public boolean isValidTx(TransactionInterface tx) throws ConsumedCoinAvailableException,
			VerifySignatureOfConsumeCoinException, CoinConsumedMultipleTimesException,
			TransactionOutputLessThanZeroException, TransactionInputSumLessThanOutputSumException {
		Set<UTXO> claimedUTXO = new HashSet<UTXO>();
		double inputSum = 0;
		double outputSum = 0;

		int i=0;
		for (InputInterface input : tx.getInputs()) {
			ConsumedCoinAvailableException.assertion(utxoPool, input);
			VerifySignatureOfConsumeCoinException.assertion(utxoPool, tx, i++, input);
			CoinConsumedMultipleTimesException.assertion(claimedUTXO, input);
			UTXO utxo = new UTXO(input);
			OutputInterface correspondingOutput = utxoPool.getTxOutput(utxo);
			inputSum += correspondingOutput.getValue();
		}

		for (OutputInterface output :tx.getOutputs()) {
			TransactionOutputLessThanZeroException.assertion(output);
			outputSum += output.getValue();
		}

		TransactionInputSumLessThanOutputSumException.assertion(outputSum, inputSum);

		return true;
	}

The same holds true for the handler code itself:

	/**
	 * Handles each epoch by receiving an unordered array of proposed transactions,
	 * checking each transaction for correctness, returning a mutually valid array
	 * of accepted transactions, and updating the current UTXO pool as appropriate.
	 */
	public Transaction[] handleTxs(Transaction[] possibleTxs) {
		List<Transaction> acceptedTx = new ArrayList<Transaction>();
		for (int i = 0; i < possibleTxs.length; i++) {
			Transaction tx = possibleTxs[i];
			if (isValidTx(tx)) {
				acceptedTx.add(tx);
				removeConsumedCoinsFromPool(tx);
				addCreatedCoinsToPool(tx);
			}
		}
		Transaction[] result = new Transaction[acceptedTx.size()];
		acceptedTx.toArray(result);
		return result;
	}

In the above case, if a transaction's input or output has an issue, all the calling code has to work with is a true or false message.  In our case, we are in a position to handle anything that goes wrong with a try/catch block:

	/**
	 * Handles each epoch by receiving an unordered array of proposed transactions,
	 * checking each transaction for correctness, returning a mutually valid array
	 * of accepted transactions, and updating the current UTXO pool as appropriate.
	 * 
	 * Don't sort the accepted transactions by fee
	 */
	@Override
	public TransactionInterface[] handleTxs(TransactionInterface[] possibleTxs) throws Exception {
		List<TransactionInterface> acceptedTx = new ArrayList<TransactionInterface>();
		for (TransactionInterface tx : possibleTxs) {
			try {
				isValidTx(tx);
				acceptedTx.add(tx);
				removeConsumedCoinsFromPool(tx);
				addCreatedCoinsToPool(tx);
			} catch (Exceptions ex) {
				Exceptions.diagnostics(ex);
			} catch (Exception ex) {
				System.out.println(ex.getMessage());
			}
		}

		Transaction[] result = new Transaction[acceptedTx.size()];
		acceptedTx.toArray(result);
		return result;
	}

As a bonus assignment, a slight variation of the handler was request where the transaction inputs need to be sorted based on the resulting fee structure:
	
	/**
	 * Handles each epoch by receiving an unordered array of proposed transactions,
	 * checking each transaction for correctness, returning a mutually valid array
	 * of accepted transactions, and updating the current UTXO pool as appropriate.
	 * 
	 * Sort the accepted transactions by fee
	 */
	@Override
	public TransactionInterface[] handleTxs(TransactionInterface[] possibleTxs) throws Exception {
		List<TransactionWithFee> acceptedTx = new ArrayList<TransactionWithFee>();
		for (TransactionInterface tx : possibleTxs) {
			try {
				isValidTx(tx);
				TransactionWithFee txWithFee = new TransactionWithFee(utxoPool,tx);
				acceptedTx.add(txWithFee);
				removeConsumedCoinsFromPool(tx);
				addCreatedCoinsToPool(tx);
			} catch (Exceptions ex) {
				Exceptions.diagnostics(ex);
			} catch (Exception ex) {
				System.out.println(ex.getMessage());
			}
		}

		Collections.sort(acceptedTx);
		TransactionInterface[] result = new Transaction[acceptedTx.size()];
		for (int i = 0; i < acceptedTx.size(); i++) {
			result[i] = acceptedTx.get(acceptedTx.size() - i - 1).tx;
		}

		return result;
	} 

# Layouts
In the movie Bourne Identify, the fire escape layout was given something like the following:
## Bourne Identity American Embassy Layout
![enter image description here](https://donsnotes.com/emergency/images/escape-plan.gif)
## Scrooge Coin Transaction Layout
Further to organizing this assignment in terms of properly written interfaces we have learned that BitCoin transaction have a certain structure, (one that was not plainly described in the initial course session):

[Scrooge Coin Transaction](https://medium.com/@zhaohuabing/scrooge-coin-c1d1d1e9fd00)

![enter image description here](https://miro.medium.com/max/700/0*MHyORgj7gX60uPXE.png)
>Every transaction has a set of inputs and a set of outputs. An input in a transaction must use a hash pointer to refer to its corresponding output in the previous transaction, and it must be signed with the private key of the owner because the owner needs to prove he/she agrees to spend his/her coins.
>
>Every output is correlated to the public key of the receiver, which is his/her ScroogeCoin address.

Further to this, it was identified that Bitcoin transactions may contain other Bitcoin transactions. This was revealed in both the supplied implementation code as exemplified in the test cases:


    private ScroogeCoinAuthority authority;
    private TransactionInterface genesiseTx;
    private UTXOPool pool;

     /**
       * @brief Scrooge has right to create coins
       * 
       *        In the first transaction, we assume that Scrooge has created 10 coins
       *        and assigned them to himself, we don’t doubt that because the
       *        system-Scroogecoin has a building rule which says that Scrooge has
       *        right to create coins.
       * 
       */

    @Before
    public void setUpHandler() throws Exception {
        authority = new ScroogeCoinAuthority();
        genesiseTx = authority.getCreator().createCoin(10);
        pool = new UTXOPool();
        UTXO utxo = new UTXO(genesiseTx.getHash(), 0);
        pool.addUTXO(utxo, genesiseTx.getOutput(0));
    }
    

In the test case below the transaction identified as **tx1** is embedded inside **tx2** and **tx3**, accordingly.

         CoinCreatorInterface Scrooge = authority.getCreator();
         CoinOwnerInterface Alice  = authority.getAlice();
         CoinOwnerInterface Bob =  authority.getBob();
         CoinOwnerInterface Mike  =  authority.getMike();
         
        // Correction: Scrooge transfer 4 coins to Alice, 6 coins to bob, no transaction fee

        TransactionInterface tx1 = new Transaction();
        tx1 = authority.addCoinForSale(tx1, genesiseTx, 0);
        tx1 = authority.addBuyer(tx1, 4, Alice);
        tx1 = authority.addBuyer(tx1, 6, Bob);
        tx1 = authority.authorizeSale(tx1, Scrooge, 0);

        /**
         * In the second transaction, Scrooge transferred 3.9 coins to Alice and 5.9
         * coins to Bob. The sum of the two outputs is 0.2 less than the input because
         * the transaction fee was 0.2 coin.
         */

        // Correction: Alice transfer 3.4 to mike, transaction fee is 4-3.4=0.6

        TransactionInterface tx2 = new Transaction();
        tx2 = authority.addCoinForSale(tx2, tx1, 0);
        tx2 = authority.addBuyer(tx2, 3.4, Mike);
        tx2 = authority.authorizeSale(tx2, Alice, 0);

        /**
         * In the third transaction, there were two inputs and one output, Alice and Bob
         * transferred 9.7 coins to mike, and the transaction fee was 0.1 coin.
         */

        // Correction: Bob transfer 5.5 to mike, transaction fee is 5-5.5=0.5

        TransactionInterface tx3 = new Transaction();
        tx3 = authority.addCoinForSale(tx3, tx1, 1);
        tx3 = authority.addBuyer(tx3, 5.5, Mike);
        tx3 = authority.authorizeSale(tx3, Bob, 0);

Finally, to make all these transactions process the transaction handler is called. In the following code block, we actually test two implementations of the TxHandlerInterface:

        /**
         * Unclaimed transaction outputs pool 
         * 
         * Another trick we need to note when doing
         * the programming assignment is that an UTXOPool is introduced to track the
         * unclaimed outputs (unspent coins), so we can know whether the corresponding
         * output of an input of the transaction is available or not.
         * 
         */

        TxHandlerInterface txHandler = new TxHandler(pool);
        TransactionInterface[] txAcceptedRx = txHandler.handleTxs(
	        new TransactionInterface[] { tx1, tx2, tx3 });
        assertEquals(txAcceptedRx.length, 3);
        assertFalse(Arrays.equals(txAcceptedRx[0].getHash(), tx2.getHash()));

        TxHandlerInterface maxFeeTxHandler = new MaxFeeTxHandler(pool);
        TransactionInterface[] maxAcceptedRx = maxFeeTxHandler.handleTxs(
	        new TransactionInterface[] { tx1, tx2, tx3 });
        assertEquals(maxAcceptedRx.length, 3);
        assertTrue(Arrays.equals(maxAcceptedRx[0].getHash(), tx2.getHash()));

# Conclusion
While some formal documentation of the interfaces defined for this assignment remains, the use of interfaces and custom exceptions, (combined with the findings of Mr. Zhao) have given us a much better idea as to how Bitcoin and Cryptocurrency technology works in real applications.
 # Implementation
 If you wish to install locally please have [Java version 8, (or better)](https://www.digitalocean.com/community/tutorials/how-to-install-java-with-apt-on-ubuntu-18-04) as well as [Maven](https://linuxize.com/post/how-to-install-apache-maven-on-ubuntu-18-04/) installed, (preferable on a [Ubuntu](https://ubuntu.com) platform).

	git clone https://github.com/perriera/CoureraBitcoin.git
	cd CoureraBitcoin
	mvn test

With Java and Maven installed the mvn test should give you output similar to the following:

	[INFO] Scanning for projects...
	[INFO] 
	[INFO] --------------------< com.zhaohuabing:ScroogeCoin >---------------------
	[INFO] Building ScroogeCoin 1.0-SNAPSHOT
	[INFO] --------------------------------[ jar ]---------------------------------
	[INFO] 
	[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ ScroogeCoin ---
	[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
	[INFO] skip non existing resourceDirectory /home/perry/projects/CoureraBitcoin/src/main/resources
	[INFO] 
	[INFO] --- maven-compiler-plugin:2.3.2:compile (default-compile) @ ScroogeCoin ---
	[INFO] Nothing to compile - all classes are up to date
	[INFO] 
	[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ ScroogeCoin ---
	[WARNING] Using platform encoding (UTF-8 actually) to copy filtered resources, i.e. build is platform dependent!
	[INFO] skip non existing resourceDirectory /home/perry/projects/CoureraBitcoin/src/test/resources
	[INFO] 
	[INFO] --- maven-compiler-plugin:2.3.2:testCompile (default-testCompile) @ ScroogeCoin ---
	[INFO] Nothing to compile - all classes are up to date
	[INFO] 
	[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ ScroogeCoin ---
	[INFO] Surefire report directory: /home/perry/projects/CoureraBitcoin/target/surefire-reports

	-------------------------------------------------------
	 T E S T S
	-------------------------------------------------------
	Running TestCasesWithTxHandler
	Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.401 sec
	Running TestCasesWithMaxFeeTxHandler
	Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.155 sec
	Running TestCasesFormal
	Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.017 sec

	Results :

	Tests run: 19, Failures: 0, Errors: 0, Skipped: 0

	[INFO] ------------------------------------------------------------------------
	[INFO] BUILD SUCCESS
	[INFO] ------------------------------------------------------------------------
	[INFO] Total time:  2.262 s
	[INFO] Finished at: 2021-08-22T15:46:53-07:00
	[INFO] ------------------------------------------------------------------------

## Comments
If you have any questions on this project please do not hesitate to contact me at perry@dmgblockchain.com, ([DMG Blockchain Solutions](https://dmgblockchain.com)) or perry.anderson@gmail.com, ([Perry Anderson](https://perryanderson.com)).
## Acknowledgements
[Coursera at Princeton University](https://www.coursera.org/) for providing such an excellent course. </br>
[Arvind Narayanan](https://www.coursera.org/learn/cryptocurrency) for his knowledge on Bitcoin and Cryptocurrency technologies.</br>
[Huabing Zhao](https://medium.com/@zhaohuabing/scrooge-coin-c1d1d1e9fd00) for his excellent work on the assignment, (especially tracking down the missing pieces of the puzzle) for us.</br>
[Matt Williams at DMG](https://github.com/mattcoding4days) for recommending the course.</br>

>
## LICENSE

[MIT License](https://github.com/perriera/extras/blob/main/LICENSE)