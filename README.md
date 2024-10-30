# Mini project - A Higher & Lower Guessing Game
def create_save_file_text(name: str, guesses: int, lowest: int, highest: int) -> str:
    """Generate text to be appended to the scores.txt file.

    Args:
        name: The name of the player.
        guesses: The number of guesses taken to guess the correct number.
        lowest: The lowest possible number, as defined by the player.
        highest: The highest possible number, as defined by the player.

    Returns:
        A string which details the game, to then be written to file.
    """
    now = datetime.now() # current day and time
    formatted_now = datetime.strftime(now, format="%H:%M:%S %d-%B-%Y")
    store_string = "Player: {}. Guesses: {}, given{} number choices. --- {}".format(
        name.title(),
        guesses,
        (highest - lowest)+1,
        formatted_now
        )
    return store_string


def write_text_to_file(filename: str, text: str, mode: str = "a"):
    """Write game summary text to a file.

    Args:
        filename: The name of the file to be opened for appending.
        text: The text to be written to file.
        mode='a': Mode has been set to append, but can be changed if needed.
    """
    with open(filename, mode=mode) as file:
        file.write(text)


def retrieve_lower_upper_numbers() -> tuple:
    """Retrieve the low/high numbers from the user.

    The user is prompted to enter the low/high numbers, and
    gets 3 attempts to do so. The try/except is to catch a situation
    where the integers are incorrectly entered. If the user exceeds the
    number of attempt then they are given 1 & 10 by default.

    Returns:
        A tuple of the low and high numbers, used in random number generation.
    """
    attempts_to_enter_valid_nums = 3
    current_attempts = 0

    while current_attempts < attempts_to_enter_valid_nums:
        current_attempts += 1
        try:
            low = int(input("Enter a lower bound: "))
            high = int(input("Enter an upper bound: "))
        except ValueError:
            print("Invalid format")
            continue
        else:
            if low < high:
                return low, high
            else:
                print("Low must be lower than high.")

    else:
        print("Attempts exceeded, defaulting to 1, 10.")
        return 1, 10


def establish_random_number(lowest: int, highest: int) -> int:
    """Return a random integer given the low and high points.

    Args:
        lowest: the lowest possible random number.
        highest: the highest possible random number.

    Returns:
        A random number between lowest and highest (inclusive of both).
    """
    return ra.randint(lowest, highest)


def main_gameplay(random_number) -> int:
    """ Gameplay loop.

    Args:
        random_number: this is the number the player is trying to guess.

    Returns:
        The number of attempts taken to guess correctly.
    """
    attempts = 1
    guess = int(input("Enter guess:"))

    while guess != random_number: # while this condition is true repeat
        attempts += 1
        if guess > random_number:
            guess = int(input("Lower..."))
        elif guess < random_number:
            guess = int(input("Higher..."))
    else:
        print("Correct!")
        return attempts


def main(filename: str) -> None: # returns filename at the end
    """Driver function to orchestrate gameplay.

    Args:
        filename: the name of the text file to be written to.
    """
    # Get the players name.
    name = input("Enter your name: ")
    if not name: # the name is empty
        print("No name entered, exiting...")
        return

    # Get the low and high numbers
    low, high = retrieve_lower_upper_numbers()
    print(f"Low is set at {low} and high is set as {high}.\n")

    # Then use these to get a random number
    random_number = establish_random_number(low, high)

    # Pass the random number through the main_gameplay function.
    score = main_gameplay(random_number)
    print(f"You took {score} attempts to guess {random_number}.")

    # Use the name and score to generate the text to the same game file.
    text_file = create_save_file_text(name, score, low, high)

    # Use filename, and text_file to save the gameplay details to file
    write_text_to_file(filename, text_file)


if __name__ == "__main__":
    filename = "scores.txt"
    main(filename)
