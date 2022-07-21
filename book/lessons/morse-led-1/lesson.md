# LED Morse Code in Python 3

This lesson is an extension of the first LED blinking experiment. It will be
done in Python3 exclusively and seeks to achieve something more complicated
with the LED. Namely, we will be creating a program that accepts input from the
user and blinks out that input in morse code on the LED. If you are unfamiliar
with morse code [this](https://en.wikipedia.org/wiki/Morse_code#:~:text=Morse%20code%20is%20a%20method,the%20inventors%20of%20the%20telegraph.) Wikipedia
article does a good job of explaining it. Basically, it is a method of
communicating where different characters (letters, numbers, etc.) are encoded
as a series of long and short duration taps, or beeps, or in our case blinks on
an LED. The short duration is called a dot and the long one is called a dash.
If you know which sequences of dots and dashes correspond to which characters,
you can communicate entire sentences this way. Indeed people used machines
called telegraphs to communicate over long distances in morse code over a
hundred years ago before phones were invented and commonplace.

**NOTE:** This lesson may be difficult especially if this is your fist exposure
to Python. You are still encouraged to try it, just don't feel bad if it's a
struggle! Also, you're always welcome to just play with the finished code.

## Parts list and Setup

This lesson requires identical parts and setup to the [hello led](../hello-led-1/lesson.md)
lesson, so please follow that setup guide and make sure that exercise works
before continuing with this one.

## Basic morse code implementation

Below is a basic implementation of a program that prompts the user to enter
something at the command line then blinks it out in morse code on the LED. It
will complain at the user if they supply a character it doesn't have a Morse
code encoding for.

This basic code only supports letters with their encodings taken from the
Wikipedia article linked previously. It is extensively commented and you are
encouraged to read it and understand how it works.

```python
# Get access to our LED
from gpiozero import LED
# We need to sleep between blinks
from time import sleep

# The pin our led is wired to. If you followed the hello led lesson exactly
# this will be 21.
LED_PIN = 21

# The duration of dots and dashes
# The standard is for a dash to be three times the duration of a dot
DOT = .25
DASH = 3 * DOT

# This dictionary maps characters to their morse code representations
ENCODING = {
    'A' : [DOT, DASH],
    'B' : [DASH, DOT, DOT, DOT],
    'C' : [DASH, DOT, DASH, DOT],
    'D' : [DASH, DOT, DOT],
    'E' : [DOT],
    'F' : [DOT, DOT, DASH, DOT],
    'G' : [DASH, DASH, DOT],
    'H' : [DOT, DOT, DOT, DOT],
    'I' : [DOT, DOT],
    'J' : [DOT, DASH, DASH, DASH],
    'K' : [DASH, DOT, DASH],
    'L' : [DOT, DASH, DOT, DOT],
    'M' : [DASH, DASH],
    'N' : [DASH, DOT],
    # Fun fact, you may be familiar with "SOS" as a call for help. Some people
    # say it stands for "save our souls" or "save our ship." In reality, it was
    # chosen because in Morse code it is
    # "DOT DOT DOT DASH DASH DASH DOT DOT DOT" which stands out, so people
    # would notice the pattern immediately. It didn't stand for anything.
    'O' : [DASH, DASH, DASH],
    'P' : [DOT, DASH, DASH, DOT],
    'Q' : [DASH, DASH, DOT, DASH],
    'R' : [DOT, DASH, DOT],
    'S' : [DOT, DOT, DOT],
    'T' : [DASH],
    'U' : [DOT, DOT, DASH],
    'V' : [DOT, DOT, DOT],
    'W' : [DOT, DASH, DASH],
    'X' : [DASH, DOT, DOT, DASH],
    'Y' : [DASH, DOT, DASH, DASH],
    'Z' : [DASH, DASH, DOT, DOT]
}


# Blink the LED for a specified duration
def blink(led, duration):
    # Turn led on
    led.on()
    # Wait for blink duration
    sleep(duration)
    # Turn led off
    led.off()
    # The standard is to have a dot duration rest between elements of one
    # character
    sleep(DOT)


# Ensures our input contains only characters we currently support
def sanitize_input(user_input):
    # First ensure all letters in the string are uppercase. We do this because
    # Morse code doesn't typically differentiate between cases, and it's easier
    # for us to tell what letter we have if we know what case it will be in
    sanitized_input = user_input.upper()

    # Iterate over characters in our uppercased input
    for character in sanitized_input:
        # If we don't have a morse code encoding for the character. . .
        if character not in ENCODING.keys():
            #. . . Tell the user about it
            print(f'The character \'{character}\' does not have a morse code '
                   'representation at this time! Either add one or only use '
                  f'valid characters: {[key for key in ENCODING.keys()]}.')
            # And return an empty string
            return ''

    # Return our sanitized and validated input
    return sanitized_input


# Takes a string and blinks the LED for all characters in the string
def process_input(sanitized_input, led):
    # Iterate over characters
    for character in sanitized_input:
        # Iterate over the blink durations for the character (found in the
        # ENCODING dictionary)
        for duration in ENCODING[character]:
            # Blink for the duration specified in the dictionary
            blink(led, duration)


# If this file is the one that Python started execution with, run this code.
# Otherwise it would start executing from the top of the file
if __name__ == '__main__':Anthony
    # Create an object in code representing our LED
    led = LED(LED_PIN)

    # Loop infinitely
    while True:
        # Get a string from the user
        # NOTE: This is a blocking operation meaning the code will halt here
        # until the user types something in and presses 'enter'
        user_input = input('Please enter your string: ')
        # Ensure the input contains only things we expect to see
        sanitized_input = sanitize_input(user_input)
        # Actually blink out the morse code corresponding to the input on the
        # LED
        process_input(sanitized_input, led)
```

## Challenges to extend the code

These are some ideas for fixes and extenstions to the basic code found above.
At the bottom of the page there will be code that implements all of these
challenges. Feel free to use it as a reference, or to compare your solutions to
mine, or just to play with a more complete implementation of Morse code.

### Easy

**Add additional Characters**

Try looking at [this](https://en.wikipedia.org/wiki/Morse_code#Mnemonics)
section of the Morse code Wikipedia article and adding support for more
characters like numbers and punctuation.

### Intermediate

**Remove the Trailing Short Rest**

You may have noticed that after the end of the user input, we sleep with the
light off for the duration of a single short rest (coming from the rest between
character elements). This isn't really a problem, but it is technically the
incorrect behavior. Can you fix it?

### Difficult

**Add a Gap Between Characters**

Standard Morse code has a gap that is 3 dot durations long between characters
in the same word. Can you add this feature to the code? For example using the
string "sos" (using '.' as a dot duration rest) we should see:

s...o...s

**Add Support for Spaces**

You may have noticed that we are missing something very importany. We don't
have spaces! In standard Morse code, a space is 7 dots long. This means that
when we see a space we want the light to be off for 7 times the dot duration.
Can you add this feature to the code?

Keep in mind that if you also added the gap between characters, we should still
only see a 7 dot rest between words. For example using the string "hello world"
(using '.' as a dot duration rest) we should see:

h...e...l...l...o.......w...o...r...l...d

As opposed to something like:

h...e...l...l...o.............w...o...r...l...d

Where we have the 3 dot rest after the 'o' then the 7 dot space then the 3 dot
duration after the space.

## Complete Code Implementation

This code implements all of the extensions and fixes proposed above. Note that
nothing about this code is the only way to do things or even necessarily the
best way to do things. One of the great things about programming is that there
are almost always many different solutions to a given probem and often no one
solution is outright the best.

```python
# Get access to our LED
from gpiozero import LED
# We need to sleep between blinks
from time import sleep

# The pin our led is wired to. If you followed the hello led lesson exactly
# this will be 21.
LED_PIN = 21

# The duration of dots and dashes
# The standard is for a dash to be three times the duration of a dot
DOT = .15
DASH = 3 * DOT

# This dictionary maps characters to their morse code representations.
ENCODING = {
    'A' : [DOT, DASH],
    'B' : [DASH, DOT, DOT, DOT],
    'C' : [DASH, DOT, DASH, DOT],
    'D' : [DASH, DOT, DOT],
    'E' : [DOT],
    'F' : [DOT, DOT, DASH, DOT],
    'G' : [DASH, DASH, DOT],
    'H' : [DOT, DOT, DOT, DOT],
    'I' : [DOT, DOT],
    'J' : [DOT, DASH, DASH, DASH],
    'K' : [DASH, DOT, DASH],
    'L' : [DOT, DASH, DOT, DOT],
    'M' : [DASH, DASH],
    'N' : [DASH, DOT],
    # Fun fact, you may be familiar with "SOS" as a call for help. Some people
    # say it stands for "save our souls" or "save our ship." In reality, it was
    # chosen because in Morse code it is
    # "DOT DOT DOT DASH DASH DASH DOT DOT DOT" which stands out, so people
    # would notice the pattern immediately. It didn't stand for anything.
    'O' : [DASH, DASH, DASH],
    'P' : [DOT, DASH, DASH, DOT],
    'Q' : [DASH, DASH, DOT, DASH],
    'R' : [DOT, DASH, DOT],
    'S' : [DOT, DOT, DOT],
    'T' : [DASH],
    'U' : [DOT, DOT, DASH],
    'V' : [DOT, DOT, DOT],
    'W' : [DOT, DASH, DASH],
    'X' : [DASH, DOT, DOT, DASH],
    'Y' : [DASH, DOT, DASH, DASH],
    'Z' : [DASH, DASH, DOT, DOT],
    # Below here are the characters I added. Could have done more, but I
    # decided these would be enough
    '0' : [DASH, DASH, DASH, DASH, DASH],
    '1' : [DOT, DASH, DASH, DASH, DASH],
    '2' : [DOT, DOT, DASH, DASH, DASH],
    '3' : [DOT, DOT, DOT, DASH, DASH],
    '4' : [DOT, DOT, DOT, DOT, DASH],
    '5' : [DOT, DOT, DOT, DOT, DOT],
    '6' : [DASH, DOT, DOT, DOT, DOT],
    '7' : [DASH, DASH, DOT, DOT, DOT],
    '8' : [DASH, DASH, DASH, DOT, DOT],
    '9' : [DASH, DASH, DASH, DASH, DOT],
    # Fun fact, Morse code didn't originally have a sequence for the period,
    # so people would send "STOP" to indicate the end of a sentence.
    '.' : [DOT, DASH, DOT, DASH, DOT, DASH],
    ',' : [DASH, DASH, DOT, DOT, DASH, DASH],
    '?' : [DOT, DOT, DASH, DASH, DOT, DOT],
    '`' : [DOT, DASH, DASH, DASH, DASH, DOT],
    '!' : [DASH, DOT, DASH, DOT, DASH, DASH],
    ' ' : [DOT, DOT, DOT, DOT, DOT, DOT, DOT]
}


# Blink the LED for a specified duration
def blink(led, duration, final=False):
    # Turn led on
    led.on()
    # Wait for blink duration
    sleep(duration)
    # Turn led off
    led.off()
    # The standard is to have a dot duration rest between elements of one
    # character, but if this was the last element of the last character we can
    # skip it
    if not final:
        sleep(DOT)


# Ensures our input contains only characters we currently support
def sanitize_input(user_input):
    # First ensure all letters in the string are uppercase. We do this because
    # Morse code doesn't typically differentiate between cases, and it's easier
    # for us to tell what letter we have if we know what case it will be in
    sanitized_input = user_input.upper()

    # Iterate over characters in our uppercased input
    for character in sanitized_input:
        # If we don't have a morse code encoding for the character. . .
        if character not in ENCODING.keys():
            #. . . Tell the user about it
            print(f'The character \'{character}\' does not have a morse code '
                   'representation at this time! Either add one or only use '
                  f'valid characters: {[key for key in ENCODING.keys()]}.')
            # And return an empty string
            return ''

    # Return our sanitized and validated input
    return sanitized_input


# This function determined if either the previous or next character is a space
def spaces_around(char_index, sanitized_input):
    # If we are on the first character we only need to check the next character
    # We know we have a next character because this function isn't called if we
    # are on the last character
    if char_index == 0:
        return sanitized_input[1].isspace()

    # If we are not on the first character we need to check if either the prior
    # or next character is a space.
    return sanitized_input[char_index - 1].isspace() or \
        sanitized_input[char_index + 1].isspace()


# Takes a string and blinks the LED for all characters in the string
def process_input(sanitized_input, led):
    # Get the index of the last character in the input. Since Python uses 0 to
    # represent the first element (as do most programming languages) we need to
    # subtract 1 from the length to get the index of the last element
    last_char_index = len(sanitized_input) - 1
    # Get the index of the last element of the representation of the character
    # in the same way
    last_elem_index = len(ENCODING[sanitized_input[last_char_index]]) - 1

    # Iterate over characters
    for char_index, character in enumerate(sanitized_input):
        # True if we are on the last character in the input
        last_char = char_index == last_char_index

        # Iterate over the blink durations for the character (found in the
        # ENCODING dictionary)
        for elem_index, duration in enumerate(ENCODING[character]):
            # True if we are on the last index of the character
            last_elem = elem_index == last_elem_index

            # If our character is a space we just sleep for the duration
            if character == ' ':
                sleep(duration)
            else:
                # If we are on the last element of the last character don't
                # sleep after turning off the LED
                if last_char and last_elem:
                    blink(led, duration, final=True)
                else:
                    blink(led, duration)

        # After each non-space character, we sleep for three dot durations
        # unless this was the last character in the input
        if character == ' ' or last_char \
                or not spaces_around(char_index, sanitized_input):
            sleep(3 * DOT)


# If this file is the one that Python started execution with, run this code.
# Otherwise it would start executing from the top of the file
if __name__ == '__main__':
    # Create an object in code representing our LED
    led = LED(LED_PIN)

    # Loop infinitely
    while True:
        # Get a string from the user
        # NOTE: This is a blocking operation meaning the code will halt here
        # until the user types something in and presses 'enter'
        user_input = input('Please enter your string: ')
        # Ensure the input contains only things we expect to see
        sanitized_input = sanitize_input(user_input)
        # Actually blink out the morse code corresponding to the input on the
        # LED
        process_input(sanitized_input, led)
```