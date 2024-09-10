# Write a program that reverse an input string?
```c
#include <stdio.h>

void print_reverse_string(char* str, int len) {
    printf("\nReversed string is: ");
    // Start from len-1 because array indices are 0-based
    for(int i = len - 1; i >= 0; i--) {
        printf("%c", str[i]);
    }
    printf("\n");  // Newline for better output formatting
}

int main()
{
    char str[] = "Hello World";
    int len = sizeof(str)/sizeof(char);  // Use strlen to get the length of the string
    
    // Print the original string and its length
    printf("String is: %s\n", str);
    printf("Length of string (excluding null terminator) is: %d\n", len);

    // Print the reversed string
    print_reverse_string(str, len);

    return 0;
}
```
# What are the networking protocols you are aware of?
SNMP
NETCONF
TCP/UDP
ICMP: ping to destination
ARP: ARP: map IP to MAC address

# ICMP
ping to destination
# ARP
mapping from IP to MAC address
# DHCP








