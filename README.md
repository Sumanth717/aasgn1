# asgn1
library IEEE;
use IEEE.std_logic_1164.all;

entity reg_file is
    generic (
        DATA_WIDTH : natural := 32;
        ADDR_WIDTH : natural := 5
    );
    port (
        clk : in std_logic;
        rst : in std_logic;

        -- Read Ports
        ra1 : in std_logic_vector(ADDR_WIDTH - 1 downto 0);
        rd1 : out std_logic_vector(DATA_WIDTH - 1 downto 0);
        ra2 : in std_logic_vector(ADDR_WIDTH - 1 downto 0);
        rd2 : out std_logic_vector(DATA_WIDTH - 1 downto 0);

        -- Write Ports
        wa1 : in std_logic_vector(ADDR_WIDTH - 1 downto 0);
        wd1 : in std_logic_vector(DATA_WIDTH - 1 downto 0);
        wa2 : in std_logic_vector(ADDR_WIDTH - 1 downto 0);
        wd2 : in std_logic_vector(DATA_WIDTH - 1 downto 0)
    );
end reg_file;

architecture behavioral of reg_file is

    signal regs : std_logic_vector(DATA_WIDTH * 32 - 1 downto 0);
    signal r15_xor_r2 : std_logic_vector(DATA_WIDTH - 1 downto 0);

begin

    -- Initialize register R31 to Year of Birth
    process(rst)
    begin
        if rst = '1' then
            regs <= (others => '0');
        end if;
    end process;

    -- R15 shall always receive R, XOR R2 at every clock cycle
    process(clk)
    begin
        if clk = '1' and clk'event then
            r15_xor_r2 <= regs(R15 * DATA_WIDTH + DATA_WIDTH - 1 downto R15 * DATA_WIDTH) xor regs(R2 * DATA_WIDTH + DATA_WIDTH - 1 downto R2 * DATA_WIDTH);
            if rst = '0' then
                regs(R15 * DATA_WIDTH + DATA_WIDTH - 1 downto R15 * DATA_WIDTH) <= r15_xor_r2;
            end if;
        end if;
    end process;

    -- Read Ports
    assign rd1 <= regs(ra1 * DATA_WIDTH + DATA_WIDTH - 1 downto ra1 * DATA_WIDTH);
    assign rd2 <= regs(ra2 * DATA_WIDTH + DATA_WIDTH - 1 downto ra2 * DATA_WIDTH);

    -- Write Ports
    process(clk)
    begin
        if clk = '1' and clk'event then
            if rst = '0' then
                if wa1 /= (others => 'X') then
                    regs(wa1 * DATA_WIDTH + DATA_WIDTH - 1 downto wa1 * DATA_WIDTH) <= wd1;
                end if;
                if wa2 /= (others => 'X') then
                    regs(wa2 * DATA_WIDTH + DATA_WIDTH - 1 downto wa2 * DATA_WIDTH) <= wd2;
                end if;
            end if;
        end if;
    end process;

end behavioral;
