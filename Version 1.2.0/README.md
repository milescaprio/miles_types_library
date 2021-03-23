Hello! Thank you for downloading the miles library.
SEE miles.h, miles.cpp, VERSION.md, and LICENSE.md
V1.2.0 In-dev

This C++ library creates a couple type classes that are manage memory themselves and have built-in functions. These types are milesString, milesInt, milesDecimal, and milesArray. Please continue reading and learn about how each of these types behave so you can use them in your programs.
If you have any questions, comments, or concerns about the library or how to use it, please do not hesitate to email me at punkymunky64@gmail.com!
Warning: Some of the classes defined in this library edit memory and are missing some error handling. Occasionally misuse of a function could cause errors that would seem unrelated. To avoid this, simply avoid relying on built-in exception handling, and read this documentation to fully understand how to operate the library.

To install this library in your programs, simply copy miles.h and miles.cpp into the same directory of your programm, and add a line on top of your program that says:
#include "miles.cpp"

-A note about size_t: size_t is just a type synonym for an int the size of a pointer; it is unsigned, and its maximum value is greater than or equal to the amount of memory on your machine. For example, if your machine is 16-bit, size_t is a two byte unsigned integer. This allows milesArrays/milesStrings to be as big as your memory can handle. If this doesn't make sense, just treat it like an unsigned int.
-A note about milesArray and milesString classes: these array classes for char and int are single-dimensional (milesString is actually just an interface around an array of char like normal). However, normal multi-dimensional arrays are stored no differently, as each dimension is actually stored end-to-end and the new indices are just calculated. Therefore, you can artificially use these classes multi-dimensionally. For example, if you wanted to have an array of size [5][5], you could store this as size [25], and if you wanted to access its index [3][1], you would convert this to one dimension with the formula [3*5+1*1], and so on.
-A note about dynamic memory allocation fails: the rule for the miles library is that array class length MUST be changed AFTER malloc exception handling; so if malloc fails and the error is caught by the user (not recommended), then the old length will be retained, however the data pointer will save a null pointer.

|--------milesString--------|
A milesString is an object that holds a pointer to a null-terminated char array.

Constructors & Initialization:
	-It can be constructed from nothing, where it will then hold just a null terminator in the data.
	-It can also be constructed from a char array (which can be a string literal), another milesString, or a std::string.
	-Finally, if you initialize it to an int value, this constructor makes an empty string with the length of the given int. The empty string's data does not contain spaces, just free memory followed by a null terminator, so if you read the string you will most likely get corrupted characters. To fix this you can use (yourobjectname).fill(' ') right afterwards.

Coded Constructor Syntax:
	milesString();
	milesString(const char *charstr);
	milesString(const std::string &str);
	milesString(const milesString &str);
	milesString(size_t newlength);

Examples of initializing a milesString:
	milesString newstring; //Creates an empty string to be overwritten later.
	milesString hello = "hello!"; //Creates a milesString and copies a char into the held data. ALSO, NOTICE THAT THIS EXPRESSION IS EQUAL TO `milesString hello("hello!");` , and the equals sign is just another way to write a line of code which initializes an object; it still calls the constructor. Same goes for the following lines.
	milesString hello2 = hello; //Creates a milesString and copies another milesString into the held data.
	milesString spacetowriteessay = 5000; //Creates a new milesString and allocates 5000 characters to be edited later. 

Destructors & Destruction:
	-When a milesString is destroyed by the destructor, it simply frees any memory where it saved its data.

Reading & Writing Functions:
	-To read the data of your milesString, use the read() member function. read() expects no arguments and returns a pointer to a char array (and since it's null-terminated, you can forget the length). Since read() actually returns the location of the string data, it is returned as const and cannot be edited. IMPORTANT: When you get the value of read(), if you save it to a pointer variable, make sure you save its value to the variable again whenever you change the length of your milesString or allocate(), as the data will be dynamically reallocated.
	-If you want to read the data of your milesString, but don't want to worry about dynamic reallocation while a copy of the data pointer is being stored, you can use the dynamicread() member function. dynamicread() expects no arguments and returns a POINTER to the pointer to the char array data, meaning if it is moved then the stored address will update. Remember to dereference this twice when accessing the data, or once when accessing the pointer to the data.  In addition, both the data and the referenced pointer to the data are marked constant (const).
	-To copy and write over the data of your milesString, use the write() member function. write() is overloaded, and expects either a milesString or a char array. It does not return a value.

Coded Reading & Writing Function Syntax:
	void write(const char *charstr);
	void write(const milesString &str);
	const char* read(void) const;
	const char* const* dynamicread(void) const;
	char* exportdata(void) const;

Reading & Writing Function Examples:
	milesString password = "sheep"; //here we initialize our password to "sheep"
	const char* const* pwordlocation = password.dynamicread(); //save our password location for easy reading
	if (passwordIsSecure(password.read())){ //we use another function from another place in the program, passwordIsSecure(), to see if our password is secure, and if not, we want to change it. This function accepts a standard char array string as an argument, so we use the read() function.
		std::cout<<(*pwordlocation)[0]<<'\n'; //outputs first password digit just for checking
		password.write("Sheep321!"); //let's change our password with the write() function. Uh-oh!! Any read() references break now, as the data has been reallocated! Luckily we have dynamicread() which is updated.
		std::cout<<(*pwordlocation)[0]<<'\n'; //outputs first password digit just for checking, and it is updated to new data.
	}
	milesString passwordforsomethingelse; //here we initialize another password
	if (!passwordHasBeenHacked(password.read())){ //we use another function from another place in the program, passwordHasBeenHacked(), to see if our password has been hacked, and if not, we want to use the same password. This function accepts a standard char array string as an argument, so we use the read() function.
		passwordforsomethingelse.write(password);  //let's copy over our old password with the write() function.
	}

Basic Editing Functions:
	-To edit a single character of your milesString, use the edit() member function. The edit() function simply takes an index of the array and changes the character value at that location. It is a synonym to the square bracket operators. It takes two arguments, an index and then a new character. It does not return a value.
	-To concatenate a string onto another, use the concat() member function. The concat() function is overloaded and expects either a milesString or a char array. It simply adds the passed string to the end of your milesString. It does not return a value.
	-If you want to insert a string into your milesString, you can use the insert() member function. The insert() function expects two arguments, the index of the insertion, and an element to insert (either a single char, another milesString, or a char array).
	-If you want to remove a part of your milesString, you can use the remove() member function. It is overloaded and expects an index of the character to remove, and optionally a length for how many characters to remove. 
	void remove(size_t index);
	void remove(size_t index, size_t length);
	void insert(size_t index, char character);
	void insert(size_t index, const milesString &str);
	void insert(size_t index, const char* charstr);

Coded Editing Function Syntax:
	void edit(size_t index, char character);
	void concat(const char *charstr);
	void concat(const milesString &str);

Basic Editing Function Examples:
	milesString helloworld = "jello"; //oops, let's edit that, typo
	helloworld.edit(0, 'h'); //edit character #0 to 'h' (indices start from 0 not 1) IMPORTANT! USE SINGLE QUOTES NOT DOUBLE (edit() expects single char's not string literals)
	helloworld.concat(" world"); //let's add on " world" to "hello" to get "hello world"

Basic Operation Functions:
	-All of the following operation functions return a variation or operated version of the object through which the function was called, so they do not affect the object, and are therefore declared as constant (const) member functions.
	-To get the length of your milesString, use the length() member function. It expects no arguments and returns a value equal to the length in characters of your milesString, which is efficiently reported due to it being held prior in a private variable.
	-To get a single character of your milesString at a certain index, use the character() member function. It simply expects the index for the character you want and returns that character. It is a synonym to the square bracket operators.
	-To take a substring of your milesString, or in other words, a section of it, use the substring() member function. The substring() function expects two arguments: start index, and length. It returns a milesString which holds the requested substring.
	-To convert your milesString to lowercase letters, use the lower() member function. The lower() function expects no arguments, and it returns a milesString which holds the requested lowercase string. Any non-alphabetical characters will remain unaffected.
	-To convert your milesString to upper letters, use the upper() member function. The upper() function expects no arguments, and it returns a milesString which holds the requested uppercase string. Any non-alphabetical characters will remain unaffected.
	-To reverse the characters in your milesString, use the reversed() member function. The reversed() function expects no arguments, and it returns a milesString which holds the requested reversed string.
	-To check if your milesString is in the format of a number, use the isnumber() member function. The isnumber() function expects no arguments and returns a boolean value that represents whether it is a valid number. Conditions for a valid number format are that the string contains of fully numerical characters, except the string may contain a single decimal point or leading minus sign.
	-To convert your milesString to a number, use the tonumber() member function. The tonumber() function reads out the milesString and converts it into a long double type value (which it returns), by taking the sum of each decimal place multiplied by its place value. If the number is of incorrect format then the script should notice and throw an exception, so it is therefore recommended that you ensure your milesString is of correct format or previously check it with isnumber(). The function expects no arguments.
	-To locate a substring or character within your milesString, use the find() member function. The find() function searches the string data from beginning to end for a string, and returns the location of the beginning of the index of the first found location, or the location after the last checked location if it cannot be found (namely, either <end> or <stringlength>). It is overloaded and expects the following arguments: a char array, milesString, or single char, then the start index, then the end index of the search. If you want the whole string to be searched and not just a range, you may omit the start and end indices, but if you include one of the two then you must additionally include the other. The end index defines the last place where the end of the substring is checked, not where the start of the substring is checked. If you are still confused about the expected arguments in the find function, the overloads are shown in the next section.

Coded Basic Operation Function Syntax:
	size_t length(void);
	size_t find(const char* charstr, size_t ptrlength) const;
	size_t find(const milesString& str) const;
	size_t find(char character) const;
	size_t find(const char* charstr, size_t ptrlength, size_t start, size_t end) const;
	size_t find(const milesString& str, size_t start, size_t end) const;
	size_t find(char character, size_t start, size_t end) const;
	size_t count(char character) const;
	size_t count(const char* charstr) const;
	size_t count(const milesString& str) const;
	size_t count(char character, size_t start, size_t end) const;
	size_t count(const char* charstr, size_t start, size_t end) const;
	size_t count(const milesString& str, size_t start, size_t end) const;
	void edit(size_t index, char character);
	char character(size_t index) const;
	bool isnumber(void);
	milesString upper(void);
	milesString lower(void);
	milesString reversed(void);
	milesString substring(size_t start, size_t length);
	long double tonumber(void) const;

Basic Operation Function Examples:
	char userinput[160];
	std::cin >> userinput;
	milesString userinputstring(userinput);
	long double userinputnumber;
	if (userinputstring.isnumber()){
		userinputnumber = userinputstring.tonumber() //extract user input into a number
	};
	//new program!: ALPHABET LETTER LOCATOR
	milesString alphabet = "abcdefghijklmnopqrstuvwxyz";
	milesString alphabetbackwords = alphabet.reversed(); //make a milesString that saves a backwards alphabet
	milesString uppercasealphabet = alphabet.upper(); //make a milesString that saves an uppercase alphabet
	milesString lmnop = alphabet.substring(11, 5); //make a milesString that saves a small section of the alphabet.
	std::cout << "what letter do you want to find?" << '\n';
	char userinput[160];
	std::cin >> userinput;
	int location = alphabet.find(userinput); //locate the requested letter in the alphabet
	if (location == alphabet.length()) { //if the letter wasnt found
		std::cout<<"could not find that letter in the alphabet..."<<'\n';
	} else {
		std::cout<<"Letter's location in the alphabet is" << location + 1<<'\n'; //tell user where their letter is in the alphabet, add one because indices in c++ (and many other languages) start at 0.
	}


Memory Functions:
	-Sometimes during coding, one needs to create empty memory in a string to edit immediately after (or sometimes later). In the milesString class, from initialization you can create empty space to work with. However, there are also member functions built into the milesString class to do similar operations.
	-If you want to extend a string so you can add values onto it, use the extend() member function. The extend() function expects one argument, the additional length in characters that you want to extend the string, and it does not return a value. In detail, when you call extend(), it allocates a new memory block of the right size for the new string data, copies the old string over, frees the old memory, and then moves the null terminator to the end of the data. The main reason to use extend() is efficiency: if you concatenated a string consisting of spaces to your milesString instead, this would cause execution time copying space characters into a place where they would just get overwritten anyway, and if you repeatedly concatenated characters onto the string one-by-one instead of extending then editing them, your milesString would repeatedly add one to the size and reallocate.
	-If you want to allocate space for editing with your milesString, in the same way you would initialize it, use the allocate() member function. This function simply frees the current string data memory, then allocates memory for a blank string of the requested size (similar to the overloaded constructor for this, but it is for an existing string). The allocate() function expects one argument, the new length of the string, and it does not return a value.
	-If you want to save your milesString into a normal char array, you can use the exportdata() member function. exportdata() allocates memory for a new char array (using malloc), copies over the data from your milesString, and returns you a pointer which becomes fully owned and editable by the user (exportdata() expects no arguments). Typically you immediately save the pointer into a char* variable. Make sure to free() the pointer when you are done.

Coded Memory Function Syntax:
	char* exportdata(void) const;
	void allocate(size_t newlength);
	void extend(size_t additionallength);

Memory Function Examples:
	alphabet = new milesString(); //here we use the new operator so we can destroy the object later to save memory, however it will naturally be destroyed if it goes out of scope. You should not use malloc as milesString has constructors and destructors.
	alphabet.allocate(26); //here, we allocate 26 characters for the full alphabet
	for (int i = 0; i < 26; i++){
		alphabet.edit(i, 'a'+i); //for i from 0 to 25, we add i to 'a' which allows us to scroll through the ASCII alphabet
	}
	std::cout<<"Alphabet length so far "<<alphabet.length()<<'\n'';
	alphabet.extend(26); //add 26 more characters in space to allow for capital letters note: it is better to allocate 52 characters from the beginning if you know ahead of time you are going to use them, as it is more efficient because the library doesn't have to reallocate and move the memory chunk.
	for (int i = 0; i < 26; i++){
		alphabet.edit(i+26 , 'A'+i) //you should see what's going on here
	}
	//ok now that we did stuff we can export it for whatever reason if we want a char array alphabet
	const char* ALPHABET = alphabet.exportdata();
	delete alphabet;

Operator Overloads:
	-In C++, there is the advantage of being able to define behavior for operators in your class. In milesString, you can use the +, =, ==, *, [], <<, and >> operators.
	-If you want to copy over one milesString to another, you could use write(), but for code cleanliness you can also use the = or << operators. The << does the same thing as it visually represents one string going into another, and as the bitshift operator it can be overloaded like this because it wouldn't have a different common use for a string. These operators also support assigning milesStrings to char arrays. The general syntax is `destination = source` or `destination << source`. Also, if you want to daisy-chain multiple of these operators together, you can how you normally would with =, but not with <<. The equals sign (=) operator overload is seperate to the equals sign used during initialization, even though they are the same character.
		Example: `milesString word; word = "sheep";` is NOT fundamentally the same as `milesString word = sheep`. Even though they do the same thing, the first calls the default constructor then uses the = operator overload, where as the second calls the char array constructor.
	-If you want to concatenate strings with a simple syntax, use the + operator. The + operator allows the concatenation of a milesString and a char array or two milesStrings (but not two char arrays), and it does NOT edit or alter the data of either argument. This is similar to the concat() member function, except the concat() function edits the object through which it was called to the new concatenated data, and returns nothing. A + operation will always return a milesString that is the 'sum' of the two arguments. 
	-If you want to multiply a string, you can use the * operator. This operator expects a milesString on the left and an int on the right, and it returns a milesString that is the multiplied version of the milesString argument. This was inspired by the built-in way Python uses the * operator on strings. In this case, multiplying means to efficiently concatenate the string with itself multiple times.
	-There is also the >> operator. This expects a milesString on the left and a char array/pointer on the right. The general rule for remembering whether to use the << operator or the >> operator is to keep the milesString on the left. This operator simply copies the data of the milesString into the char array/pointer. It is similar to exportdata(), but the char array/pointer must have been previously been allocated memory, as it writes to its location. Make sure you have enough memory allocated in the char array/pointer you pass to the operator, as there is no bounds checking, and it would scribble on your memory!
	-Most importantly, you can use the [] operators to get the character at a certain index of your milesString. These are overloaded to return the character or edit the element there. The brackets also accept negative indices, which index from the back of the string.
	-Finally, you can check for equality of two milesStrings with the == operator. They are equal if the two strings are the same length and each character is the same.

Coded Operator Overload Syntax:
	friend milesString operator+(milesString str1, milesString str2);
	friend milesString operator+(milesString str, const char *charstr);
	friend milesString operator+(const char *charstr, milesString str);
	
	milesString& operator=(const milesString &source);
	milesString& operator=(const char *charstr);
	milesString& operator<<(const milesString &source);
	milesString& operator<<(const char *charstr);

	milesString operator*(size_t count);

	char* operator>>(char *charstr);
	char operator[](size_t index) const;
	char& operator[](size_t index);
	
Operator Examples:
	milesString hello = "Hello, ";
	milesString world = "World! ";
	milesString helloworld;
	helloworld << hello+world; //Take the 'sum' of hello and world and feed the result into helloworld. Can alternatively use = operator.
	helloworld[14] = '!'; //change the last space to an exclamation point
	milesString helloworldmanytimes = helloworld*50; //Make a new milesString called helloworldmanytimes and initialize it to "Hello, World! " chained 50 times
	char userinput[160]; //make a standard char array for cin
	std::cin >> userinput;
	if (userinput == "hello"){
		std::cout << helloworldmanytimes.read() << '\n'; //If user says hello, say hello back
	}






|--------milesArray--------|
A milesArray is an object that holds a pointer to an int array. It is an improved C int array, but can only store one dimension. Its implementation is similar to that of milesString's, but remember that it is NOT null terminated, so in some places when converting to and from an int array, the length must be provided.

Constructors & Initialization:
	-A milesArray can be constructed from nothing, where it will then by default hold just a 0 in the data.
	-A milesArray can also be constructed from an int array and its length or another milesArray.
	-A milesArray can be constructed from an initializer list or std::vector; if you want to construct it from a std::array or std::list, just get the data pointer of it with .begin() and also pass the length.
	-Finally, if you initialize it to a integer (size_t) value, this constructor makes an empty array with the length of the given integer (size_t). The empty arrays's data does not contain zeros, just free memory, so if you read the array you will most likely get corrupted characters. To fix this you can use (yourobjectname).fill(0) right afterwards.

Coded Constructor Syntax:
	milesArray();
	milesArray(const int *vect);
	milesArray(const milesArray &arr);
	milesArray(const std::initializer_list<int> &list);
	milesArray(const std::vector<int> &list);
	milesArray(int newlength);

Examples of initializing a milesArray:
	milesArray newarray; //Creates an empty array to be overwritten later.
	milesArray fibonacci = {1,1,2,3,5,8}; //Creates a milesArray by copying an initializer list into its data.
	milesArray fibonacci2 = fibonacci; //Creates a milesArray and copies another milesArray into the held data.
	milesArray spacetostorenumbers = 5000; //Creates a new milesArray and allocates 5000 ints to be edited later. 

Destructors & Destruction:
	-When a milesArray is destroyed by the destructor, it simply frees any memory where it saved its data.

Reading & Writing Functions:
	-To read the data of your milesArray, use the read() member function. read() expects no arguments and returns a pointer to a int array, however, the pointer returned does not know the length of the array, and it must be passed seperately. Since read() actually returns the location of the array data, it is returned as const and cannot be edited. IMPORTANT: When you get the value of read(), if you save it to a pointer variable, make sure you save its value to the variable again whenever you change the length of your milesArray or allocate(), as the data will be dynamically reallocated.
	-If you want to read the data of your milesArray, but don't want to worry about dynamic reallocation while a copy of the data pointer is being stored, you can use the dynamicread() member function. dynamicread() expects no arguments and returns a POINTER to the pointer to the int array data, meaning if it is moved then the stored address will update. Remember to dereference this twice when accessing the data, or once when accessing the pointer to the data. In addition, both the data and the referenced pointer to the data are marked constant (const).
	-To copy and write over the data of your milesArray, use the write() member function. write() is overloaded, and expects either a milesArray or an int array and its length. It does not return a value.

Coded Reading & Writing Function Syntax:
	void write(const int *vect);
	void write(const milesArray &arr);
	const int* read(void) const;
	const int* const* dynamicread(void) const;

	int* exportdata(void) const;

Reading & Writing Function Examples:
	milesArray pin = {1,2,3,4}; //here we initialize our PIN to 1,2,3,4
	const int* const* pinlocation = pin.dynamicread(); //save our pin location for easy reading
	if (pinIsSecure(pin.read(), pin.length())){ //we use another function from another place in the program, pinIsSecure(), to see if our pin is secure, and if not, we want to change it. This function expects a standard int array pointer and its length as an argument, so we use the read() and length() functions. However a function could be written to expect milesArrays so the argument wouldn't have to be converted and the length wouldn't have to be passed.
		std::cout<<(*pinlocation)[0]<<'\n'; //outputs first pin digit just for checking
		pin.write({9,8,7,6,5,4,3,2}, 6); //let's change our pin with the write() function. Uh-oh!! Any read() references break now, as the data has been reallocated! Luckily we have dynamicread() which is updated.
		std::cout<<(*pinlocation)[0]<<'\n'; //outputs first pin digit just for checking, and it is updated to new data.
	}
	milesArray pinforsomethingelse; //here we initialize another pin
	if (!pinHasBeenHacked(pin.read(), pin.length())){ //we use another function from another place in the program, pinHasBeenHacked(), to see if our pin has been hacked, and if not, we want to use the same pin. This function accepts a standard char array Array as an argument, so we use the read() function.
		pinforsomethingelse.write(pin);  //let's copy over our old pin with the write() function.
	}

Basic Editing Functions:
	-To edit a single element of your milesArray, use the edit() member function. The edit() function simply takes an index of the array and changes the value at that location. It is a synonym to the square bracket operators. It takes two arguments, an index and then a new value. It does not return a value. 
	-To concatenate an array onto another, use the concat() member function. The concat() function is overloaded and expects either a milesArray or an int array pointer and its length. It simply adds the passed string to the end of your milesArray. It does not return a value.
	-If you want to insert an item into your milesArray, you can use the insert() member function. The insert() function expects two arguments, the index of the insertion, and an element to insert (either a single int, another milesArray, or an int array and then an additional argument for its length).
	-If you want to remove an item from your milesArray, you can use the remove() member function. It is overloaded and expects an index of the element to remove, and optionally a length for how many elements to remove. 
	-If you want to swap two elements in your milesArray, you can use the swap() member function. The swap() function expects two arguments, the indices of the two swapping elements.
	-If you want to append a single element to your milesArray, you can use the append() member function. The append() function expects one argument, the element to append. If you want to another milesArray, use the concat() member function.

Coded Editing Function Syntax:
	void edit(size_t index, int element);
	void concat(const int *vect, size_t vectlength);
	void concat(const milesArray &arr);
	void append(int item);
	void swap(size_t index1, size_t index2);
	void remove(size_t index);
	void remove(size_t index, size_t length);
	void insert(size_t index, int element);
	void insert(size_t index, milesArray arr);
	void insert(size_t index, const int* vect, int vectlength);

Basic Editing Function Examples:
	milesArray favoritenumbers = 2; //make new array of length 2 called favoritenumbers. This will represent our favorite numbers, from favoritest to least favoritest. We will edit this list until we are happy with it.
	favoritenumbers.edit(0, 19); //edit character #0 to 19 (indices start from 0 not 1)
	favoritenumbers.edit(1, 17); //edit character #1 to 17
	favoritenumbers.concat(friendsfavoritenumbers); //let's add on our friend's favorite numbers to our list
	favoritenumbers.append(5); //I like 5 also.
	favoritenumbers.insert(0, 12); //I FORGOT 12!, lets put that first.
	favoritenumbers.swap(1,2); //actually I like 17 more than 19.
	favoritenumbers.remove(3, friendsfavoritenumbers.length()); //actually this is my list not my friend's.	

Basic Operation Functions:
	-All of the following operation functions return a variation or operated version of the object through which the function was called, so they do not affect the object (your milesArray), and are therefore declared as constant (const) member functions.
	-To get the length of your milesArray, use the length() member function. It expects no arguments and returns a value equal to the length in elements of your milesArray, which is efficiently reported due to it being held prior in a private variable.
	-To get a single element of your milesArray at a certain index, use the element() member function. It simply expects the index for the element you want and returns that element's value. It is a synonym to the square bracket operators.
	-If you want to use a shortcut for the first and last elements of your milesArray, you can use the front() and back() member functions, which neither need any arguments and respectively return the first and last elements of the array.
	-To take a subarray of your milesArray, or in other words, a section of it, use the subarray() member function. The subarray() function expects two arguments: start index, and length. It returns a milesArray which holds the requested subarray.
	-To reverse the elements in your milesArray, use the reversed() member function. The reversed() function expects no arguments, and it returns a milesArray which holds the requested reversed array.
	-To order the elements in your milesArray based on value, use the ascending() and descending() member functions. These each return a milesArray that has been insertion-sorted. Neither expect any arguments.
	-To shuffle the elements in your array randomly, you can use the shuffle() member function. It expects an argument, the seed of the random shuffle, and typically time(NULL) should be passed to generate a seed based on the time (make sure to include a time library like <ctime> or <time.h>). If you are coding an arduino you could use the built-in millis() function instead of time(NULL). If a seed is omitted then shuffle() will give the same output every time, which defeats the point.
	-To locate a subarray or element within your milesArray, use the find() member function. The find() function searches the string data from beginning to end for a string, and returns the location of the beginning of the index of the first found location, or the location after the last checked location if it cannot be found (namely, either <end> or <stringlength>). It is overloaded and expects the following arguments: an int array and its length, milesArray, or single int, then the start index, and then the end index of the search. If you want the whole array to be searched and not just a range, you may omit the start and end indices, but if you include one of the two then you must additionally include the other. The end index defines the last place where the end of the subarray is checked, not where the start of the subarray is checked. If you are still confused about the expected arguments in the find function, the overloads are shown in the next section.

Coded Basic Operation Function Syntax:
	size_t length(void);
	size_t find(const int* vect, size_t ptrlength) const;
	size_t find(const milesArray& arr) const;
	size_t find(int element) const;
	size_t find(const int* vect, size_t ptrlength, size_t start, size_t end) const;
	size_t find(const milesArray& arr, size_t start, size_t end) const;
	size_t find(int element, size_t start, size_t end) const;
	size_t count(const int* vect, size_t ptrlength) const;
	size_t count(const milesArray& arr) const;
	size_t count(int element) const;
	size_t count(const int* vect, size_t ptrlength, size_t start, size_t end) const;
	size_t count(const milesArray& arr, size_t start, size_t end) const;
	size_t count(int element, size_t start, size_t end) const;
	int element(size_t index) const;
	milesArray reversed(void) const;
	milesArray ascending(void) const;
	milesArray descending(void) const;
	milesArray shuffled(void) const;
	milesArray shuffled(unsigned long seed) const;
	milesArray subarray(size_t start, size_t length) const;
	milesString outputstring(void) const;
	int front(void) const;
	int back(void) const;

Basic Operation Function Examples:
	milesArray dataset = {9, 5, 14, 13, 12, 9, 15, 12, 13, 11, 6, 10};
	std::cout<<"Index of First 12:"<<dataset.find(12)<<'\n';
	std::cout<<"Count of 12's"<<dataset.count(12)<<'\n';
	std::cout<<"First element"<<dataset.front()<<'\n';
	std::cout<<"Last element:"<<dataset.back()<<'\n';
	std::cout<<"Reversed:"<<dataset.reversed().outputstring().read()<<'\n'; //this is how we output a milesArray to cout: we take the milesArray we want, use the outputstring() to get a milesString formatted version, and then convert that to a char array/pointer for the cout to output. Many .()'s are intimidating but are quite simple, really.
	std::cout<<"Ordered Ascending:"<<dataset.ascending().outputstring().read()<<'\n';
	std::cout<<"Ordered Descending:"<<dataset.descending().outputstring().read()<<'\n';
	std::cout<<"Shuffled:"<<dataset.shuffled(time(NULL)).outputstring().read()<<'\n';
	std::cout<<"Length:"<<dataset.length()<<'\n';
	std::cout<<"3rd Element:"<<dataset.element(2)<<'\n'; //can also use dataset[2]
	std::cout<<"Subarray:"<<dataset.subarray(3,3).outputstring().read()<<'\n'; //makes subarray starting from index 3 (which is a 13) and 3 long (so it goes to and includes index 5, a 9);

Aggregate Functions:
	-Aggregate functions are operations that reduce an set of numbers down to one number that represents a statistic. They have been implemented in milesArray, and all of the aggregate functions return a value of type int, except for mean() and median() which return long double's. In addition, all of them are overloaded to either expect no arguments or a start and end index (which, as standard, is start-inclusive and end-exclusive).
	-If you want to take the average or mean of your milesArray, use the mean() member function.
	-If you want to take the median of your milesArray, use the median() member function.
	-If you want to take the sum of all the elements in your milesArray, use the sum() member function.
	-If you want to take the product of all the elements in your milesArray, use the product() member function.
	-If you want to take the minimum element value of your milesArray, use the min() member function.
	-If you want to take the maximum element value of your milesArray, use the max() member function.
	-If you want to take the range of your milesArray (which is the maximum value minus the minimum value), use the range() member function.
	-If you want to take the most common element or mode of your milesArray, use the mode() member function.

Coded Aggregate Function Syntax:
	int product(void) const;
	int product(size_t start, size_t end) const;
	int sum(void) const;
	int sum(size_t start, size_t end) const;
	double mean(void) const;
	double mean(size_t start, size_t end) const;
	double median(void) const;
	double median(size_t start, size_t end) const;
	int min(void) const;
	int min(size_t start, size_t end) const;
	int max(void) const;
	int max(size_t start, size_t end) const;
	int mode(void) const;
	int mode(size_t start, size_t end) const;
	int range(void) const;
	int range(size_t start, size_t end) const;

Aggregate Function Examples:
	milesArray dataset = {9, 5, 14, 13, 12, 9, 15, 12, 13, 11, 6, 10};
	std::cout<<"Mean:"<<dataset.mean()<<'\n';
	std::cout<<"Median:"<<dataset.median()<<'\n';
	std::cout<<"Sum:"<<dataset.sum()<<'\n';
	std::cout<<"Product:"<<dataset.product()<<'\n';
	std::cout<<"Min:"<<dataset.min()<<'\n';
	std::cout<<"Max:"<<dataset.max()<<'\n';
	std::cout<<"Range:"<<dataset.range()<<'\n';
	std::cout<<"Mode:"<<dataset.mode()<<'\n';

	std::cout<<"Mean without endpoints:"<<dataset.ascending.mean(1,11);

Memory Functions:
	-Sometimes during coding, one needs to create empty memory in an array to edit immediately after (or sometimes later). In the milesArray class, from initialization you can create empty space to work with. However, there are also member functions built into the milesArray class to do similar operations.
	-If you want to extend an array so you can add values onto it, use the extend() member function. The extend() function expects one argument, the additional length that you want to extend the array, and it does not return a value. In detail, when you call extend(), it allocates a new memory block of the right size for the new array data, copies the old array over, then frees the old memory. The main reason to use extend() is efficiency: if you concatenated an array consisting of zeros to your milesArray instead, this would cause execution time copying zeros into a place where they would just get overwritten anyway, and if you repeatedly appended elements onto the array one-by-one instead of extending then editing them, your milesArray would repeatedly add one to the size and reallocate.
	-If you want to allocate space for editing with your milesArray, in the same way you would initialize it, use the allocate() member function. This function simply frees the current array data memory, then allocates memory for a blank array of the requested size (similar to the overloaded constructor for this, but it is for an existing array). The allocate() function expects one argument, the new length of the array, and it does not return a value.
	-If you want to save your milesArray into a normal int array, you can use the exportdata() member function. exportdata() allocates memory for a new int array (using malloc), copies over the data from your milesArray, and returns you a pointer which becomes fully owned and editable by the user (exportdata() expects no arguments). Typically you immediately save the pointer into an int* variable. Make sure to free() the pointer when you are done.

Coded Memory Function Syntax:
	int* exportdata(void) const;
	void allocate(size_t newlength);
	void extend(size_t additionallength);

Memory Function Examples:
	milesArray fibonacci;
	fibonacci.allocate(10); //allocate 10 elements
	fibonacci[0] = 1;
	fibonacci[1] = 1;
	for (int i = 2; i < 10; i++){ //fill elements
		fibonacci[i] = fibonacci[i-1] + fibonacci[i-2];
	}
	fibonacci.extend(5); //decided i want more
	for (int i = 10; i < 15; i++){ //fill more elements
		fibonacci[i] = fibonacci[i-1] + fibonacci[i-2];
	}
	//did a bunch of operations to array now time to export because need standard int array/pointer for other stuff
	char* fibonacciCArr = fibonacci.exportdata();
	//do stuff with fibonacciCArr here
	free(helloworldcharstr);

Array Math Functions:
	-In the milesArray class, you can add, subtract, multiply, and divide each corresponding element of milesArrays together with the add(), subtract(), multiply(), and divide() member functions. These all behave in the same way (except for the difference in which operation is being done). They expect one argument, the array that your milesArray will operate with (which must be of the same length as your milesArray), which can be a milesArray, initializer list, or an int array and its length. The functions directly edit your milesArray and do not return values.
	
Array Math Function Examples:
	milesArray arrayone = {1,2,3,4,5};
	milesArray arraytwo = {2,3,4,5,6};
	arrayone.add(arraytwo); //changes arrayone to {3,5,7,9,11}
	arrayone = {1,2,3,4,5}; //reset it
	arrayone.multiply(arraytwo); //changes arrayone to {2,6,12,20,30}
	arrayone = {1,2,3,4,5}; //reset it
	arrayone.subtract(arraytwo); //changes arrayone to {-1,-1,-1,-1,-1}
	arrayone = {1,2,3,4,5}; //reset it
	arrayone.divide(arraytwo); //changes arrayone to {1,1,1,1,1} (decimals are ignored as milesArray is just ints)

Coded Array Math Function Syntax:
	void add(const milesArray& arr);
	void add(const int *vect, size_t vectlength);
	void add(std::initializer_list<int> list);
	void subtract(const milesArray& arr);
	void subtract(const int *vect, size_t vectlength);
	void subtract(std::initializer_list<int> list);
	void multiply(const milesArray& arr);
	void multiply(const int *vect, size_t vectlength);
	void multiply(std::initializer_list<int> list);
	void divide(const milesArray& arr);
	void divide(const int *vect, size_t vectlength);
	void divide(std::initializer_list<int> list);

Operator Overloads:
	-In C++, there is the advantage of being able to define behavior for operators in your class. In milesArray, you can use the +, =, ==, *, [], <<, and >> operators.
	-If you want to copy over one milesArray to another, you could use write(), but for code cleanliness you can also use the = or << operators. The << does the same thing as it visually represents one string going into another, and as the bitshift operator it can be overloaded like this because it wouldn't have a different common use for a string. These operators also support assigning milesArrays to char arrays. The general syntax is `destination = source` or `destination << source`. Also, if you want to daisy-chain multiple of these operators together, you can how you normally would with =, but not with <<. The equals sign (=) operator overload is seperate to the equals sign used during initialization, even though they are the same character.
		Example: `milesArray word; word = "sheep";` is NOT fundamentally the same as `milesArray word = sheep`. Even though they do the same thing, the first calls the default constructor then uses the = operator overload, where as the second calls the char array constructor.
	-If you want to concatenate strings with a simple syntax, use the + operator. The + operator allows the concatenation of a milesArray and a char array or two milesArrays (but not two char arrays), and it does NOT edit or alter the data of either argument. This is similar to the concat() member function, except the concat() function edits the object through which it was called to the new concatenated data, and returns nothing. A + operation will always return a milesArray that is the 'sum' of the two arguments. 
	-If you want to multiply a string, you can use the * operator. This operator expects a milesArray on the left and an int on the right, and it returns a milesArray that is the multiplied version of the milesArray argument. This was inspired by the built-in way Python uses the * operator on strings. In this case, multiplying means to efficiently concatenate the string with itself multiple times.
	-There is also the >> operator. This expects a milesArray on the left and a char array/pointer on the right. The general rule for remembering whether to use the << operator or the >> operator is to keep the milesArray on the left. This operator simply copies the data of the milesArray into the char array/pointer. It is similar to exportdata(), but the char array/pointer must have been previously been allocated memory, as it writes to its location. Make sure you have enough memory allocated in the char array/pointer you pass to the operator, as there is no bounds checking, and it would scribble on your memory!
	-Most importantly, you can use the [] operators to get the element at a certain index of your milesArray. These are overloaded to return the element or edit the element there. The brackets also accept negative indices, which index from the back of the array.
	-Finally, you can check for equality of two milesArrays with the == operator. They are equal if the two strings are the same length and each character is the same.

Coded Operator Overload Syntax:
	friend milesArray operator+(milesArray arr1, const milesArray &arr2);

	friend bool operator==(const milesArray& arr1, const milesArray& arr2);

	milesArray& operator=(const milesArray& arr)
	milesArray& operator<<(const milesArray& arr)
	int* operator>>(int* vect) const;
	milesArray operator*(size_t count);
	int operator[](size_t index) const;
	int& operator[](size_t index);
	
Operator Examples:
	milesArray one2345 = {1,2,3,4,5};
	milesArray six78910 = {6,7,8,9,10};
	milesArray toTen;
	toTen << one2345+six78910; //concatenate 12345 and 678910 to get a full array
	toTen << toTen*10; //make it count 10 times by multiplying it by ten
	toTen[-1] = 100; //change last element to 100
	toTen[0] = 5; //change first element to 5

|--------milesInt and milesDecimal--------|
milesInt and milesDecimal classes are improved int and long double objects, respectively. They are very similar to normal int and long doubles, but they have the advantages of string conversion operations.

Construction:
	milesInt and milesDecimal's can be constructed from their base types (int and long double), from other milesInt and milesDecimal's, and from their default constructors, where they take the value of zero.

Construction Examples:
	milesInt constructed1 = 12;
	milesDecimal constructed2 = constructed1;
	milesDecimal constructed3 = 15.6;
	milesInt constructed4 = constructed3;
	milesInt constructed5;

Coded Constructor Syntax:
	milesInt();
	milesInt(int initvalue);
	milesInt(const milesInt& initvalue);
	milesInt(const milesDecimal& initvalue);
	milesDecimal();
	milesDecimal(int initvalue);
	milesDecimal(const milesInt& initvalue);
	milesDecimal(const milesDecimal& initvalue);

Reading and Writing:
	-milesInt and milesDecimal's are objects, and when their core value is needed, the read() member function should be used. read() no arguments and returns the basic int/long double type.
	-To change the value of a milesInt or milesDecimal, use the write() function (a synonym to the = operator). write() expects one argument, the new value (which can be a int/long double type or another milesInt/milesDecimal object), and sets the current value to it.

Reading and Writing Examples:
	milesInt five;
	five.write(5); //could also use five = 5
	std::cout<<five.read();

Coded Reading and Writing Syntax:
	int read(void) const;
	void write(int newvalue);
	void write(const milesInt& newvalue);

String Conversion Functions:
	-To get the length of a milesInt/milesDecimal in digits (including the negative sign), use the length() member function. length() expects no arguments and returns the length of the milesInt/milesDecimal in characters. If you are using a milesDecimal, length() optionally expects an argument stating the number of decimals to account for, as floating-point numbers don't know this themselves, and if any decimals are accounted for 1 is added to the length for the decimal point character.
	-To convert a milesInt/milesDecimal to a string, use the tostring() member function. tostring() returns a milesString, which can be read with a const char array pointer with .read(), or copied to a char array/other milesString with <<. If you are using a milesDecimal, tostring() optionally expects an argument stating the number of decimals to account for, as floating-point numbers don't know this themselves.
	-To assign a string's numberical value to a milesInt/milesDecimal, use the writefromstring() member function. writefromstring() expects one argument, a string (a char array or milesString), then it converts that to a numerical value, and writes it to your milesInt/milesDecimal.

String Conversion Function Examples:
	milesDecimal pi = 3.14159;
	std::cout<<"pi integer string length: "<<pi.length()<<'\n'<<"pi string length: "<<pi.length(5)<<'\n';
	milesString pistr = pi.tostring(5); //makes a string pistr that contains "3.14159"
	milesString mystrnum = "-35.9";
	milesDecimal frommystrnum;
	frommystrnum.writefromstring(mystrnum); //converts the string "-35.9" to a numerical value into frommystrnum

Coded String Conversion Syntax:
	size_t length(void) const;
	size_t length(size_t decimals) const;
	milesString tostring(void) const;
	milesString tostring(int decimals) const;
	void writefromstring(const milesString& newvaluestr);
	void writefromstring(const char* charstr);

Operator Overloads:
	-milesInt and milesDecimal are overloaded for the following operators to behave the same way native types would: *,+,/,-,%,+=,-=,++,--,*=,/=,%=,==,=,>,<,>=,<=,!=
	-milesInt and milesDecimal are also overloaded for ^ and ^=, but not in the same way native types are: instead of bit calculations, they put the number to a power.
	-All overloads support two milesInt/milesDecimal objects, or one and one native type. If you would like to operate with the standard operating functions, remember that you can easily convert milesInt/milesDecimal objects with .read().
	-FLOATING POINT DIVISION: to ensure that your program does not use integer division in a case where you would expect floating point or vice versa, make sure to make both arguments of the / operator the correct type. One way to do this is with a c-style cast.

Operator Overload Examples:
	milesInt two = 2;
	std::cout<<two*two<<'\n'; //operation between two milesInt
	std::cout<<2*two<<'\n'; //operation between one int and one milesInt
	std::cout<<2.0/two<<'\n'; //operation betweem one long double and one milesInt: BEHAVIOR HARD TO PREDICT!: based on implicit conversion precedence
	std::cout<<2.0/two<<'\n'; //operation betweem one long double and one casted milesInt: improved predictable behavior
	std::cout<<two^two<<'\n'; //exponential operation between two milesInts's

|-------Other library functions-------|

milesintarrcpy() and mileschararrcpy():
	-These are two functions which copy int and char arrays respectively, element by element. They do not return a value, and they expect first a destination pointer, then a source pointer, then the length to copy. Make sure the copy length is not greater than the size of the destination pointer, as this will result in a buffer overrun and scribbling on memory. If you are copying two char arrays, and both are null-terminated, and the destination size is greater than or equal to the source size, then you can use milesstrlen(source) as the copy-length argument.

milesintarrcat() and mileschararrcat():
	-These are two functions which concatenate int and char arrays respectively, element by element. They do not return a value, and they expect a destination pointer, a source pointer, a copy length, and a concatenation length. Make sure the sum of the copy length and the concatenation length is not greater than the size of the destination pointer, as this will result in a buffer overrun and scribbling on memory. In the copy length, use the current size of your array, and in the concatenation length, use the size of the source array to concatenate. If you are using a null-terminated char array as the source pointer, you can use milesstrlen(source) as the copy-length argument, and if your are using a null-terminated char array as the destination pointer, you can use milesstrlen(destination) as the concatenation-length argument.

milesstrlen():
	-This is a function which counts the amount of characters in a char array before the null terminator. It expects a char array pointer to measure the length of and it returns a size_t count of the characters before the null-terminator. It is extremely similar to <string>'s strlen().

Coded Syntax:

void milesintarrcpy(int* dest, const int* source, size_t cpylength);
void milesintarrcat(int* dest, const int* source, size_t currentlength, size_t catlength);
void mileschararrcpy(char* dest, const char* source, size_t cpylength);
void mileschararrcat(char* dest, const char* source, size_t currentlength, size_t catlength);
size_t milesstrlen(const char* charstr);

Copyright (C) Miles C/PunkyMunky64 2021