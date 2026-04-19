# Medicine_web_data
### Navpill section
 const NavPill = ({ children, isActive }) => {
  const base =
    "px-5 py-2 rounded-full font-semibold transition-all duration-300";

  const active = "bg-blue-600 text-white";

  const inactive = "text-gray-700 hover:bg-blue-100";

  return (
    <span className={`${base} ${isActive ? active : inactive}`}>
      {children}
    </span>
  );
};
