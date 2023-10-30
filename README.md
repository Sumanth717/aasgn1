# asgn1
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity Register_File is
    Port (
        clk : in STD_LOGIC;
        reset : in in STD_LOGIC;
        read_port1_en : in STD_LOGIC;
        read_port2_en : in STD_LOGIC;
        write_port1_en : in STD_LOGIC;
        write_port2_en : in STD_LOGIC;
        write_data1 : in STD_LOGIC_VECTOR(31 downto 0);
        write_data2 : in STD_LOGIC_VECTOR(31 downto 0);
        read_data1 : out STD_LOGIC_VECTOR(31 downto 0);
        read_data2 : out STD_LOGIC_VECTOR(31 downto 0)
    );
end Register_File;

architecture Behavioral of Register_File is
    type Register is array(0 to 31) of STD_LOGIC_VECTOR(31 downto 0);
    signal registers : Register;
begin
    process (clk, reset)
    begin
        if reset = '1' then
            -- Initialize the registers, setting R31 with your birthdate
            registers(31) <= "DD/MM/YYYY";  -- Replace with actual date
        elsif rising_edge(clk) then
            if write_port1_en = '1' then
                -- Write to registers, including the XOR operation for R15
                if write_data1 = registers(15) then
                    registers(15) <= write_data1 XOR write_data2;
                else
                    registers(15) <= write_data1;
                end if;
            end if;
            if write_port2_en = '1' then
                -- Write to registers
                -- You can implement the write to registers R16 to R0 here
            end if;
            if read_port1_en = '1' then
                -- Read from registers R31 to R16
                read_data1 <= registers(to_integer(unsigned(write_data1(4 downto 0))));
            end if;
            if read_port2_en = '1' then
                -- Read from registers R15 to R0
                read_data2 <= registers(to_integer(unsigned(write_data2(4 downto 0)));
            end if;
        end if;
    end process;
end Behavioral;
