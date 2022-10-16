Objects and Inheritance

## Inheritance
Why?
- Code re-use: write one, use multiple times
	- Efficient
	- One set of rules
	- One place to fix/edit should there be bugs/requirement changes

#### Example:
```java
public class USAResident {
	private String SSN;
	public void setSSN (String number) {
		this.SSN = number;
	}
	public String getSSN() {
		return this.SSN;
	}
}

public class StateResident extends USAResident {
	private double salesTax = .05;
}

public class LocalResident extends StateResident {
	private double salesTax = .09;
	public double getSalesTax() {
		return this.salesTax;
	}
}

public static void main(String[] args) {
	LocalResident steve = new LocalResident();
	steve.setSSN(“123456789”);
	System.out.println( steve.getSSN() + “  “ + steve.getSalesTax()); 
}
```